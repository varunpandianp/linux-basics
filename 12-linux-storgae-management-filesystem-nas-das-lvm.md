# Linux Storage Management — DevOps Notes

## 1. Introduction to Linux Storage

Linux storage management involves identifying disks, creating partitions, formatting filesystems, mounting storage, managing disk capacity, and expanding storage using LVM.

### Storage workflow

Disk → Partition → Filesystem → Mount Point → Application Data

Example:

- Disk: `/dev/sdb`
- Partition: `/dev/sdb1`
- Filesystem: `ext4`
- Mount point: `/data`

A partition is optional in some configurations. A filesystem can also be created directly on a whole disk or an LVM logical volume.

## 2. Essential Linux Storage Commands

| Command | Purpose |
|---|---|
| `lsblk` | List disks and partitions |
| `lsblk -f` | Show filesystem types, UUIDs and mount points |
| `sudo fdisk -l` | Display partition tables |
| `sudo gdisk -l /dev/sdb` | Inspect GPT partition table |
| `sudo blkid` | Show filesystem UUIDs and types |
| `df -h` | Show filesystem capacity in human-readable format |
| `df -hT` | Show filesystem capacity and type |
| `df -i` | Show inode usage |
| `du -sh /var/log` | Show total directory size |
| `findmnt` | Show mounted filesystems |
| `mount` | Display mounts or mount a filesystem |
| `umount /data` | Unmount a filesystem |
| `cat /etc/fstab` | Display persistent mount configuration |
| `sudo pvs` | Display LVM physical volumes |
| `sudo vgs` | Display LVM volume groups |
| `sudo lvs` | Display LVM logical volumes |

Example:

```bash
lsblk
lsblk -f
sudo fdisk -l
df -hT
findmnt
sudo blkid
```

**Important:** `df` reports filesystem usage; `du` measures space used by files and directories.

## 3. Disk Partitioning

Partitioning divides a disk into logical sections.

Example:

```text
/dev/sdb
├── /dev/sdb1    5G
├── /dev/sdb2   10G
└── /dev/sdb3    5G
```

Reasons for partitioning:

- Separate operating system and application data.
- Isolate logs from application files.
- Allocate space for swap or specialized filesystems.
- Simplify storage administration.

### MBR vs GPT

MBR and GPT are partition table schemes, not filesystems.

| Feature | MBR | GPT |
|---|---|---|
| Full name | Master Boot Record | GUID Partition Table |
| Partition limit | Four primary entries | Commonly 128 entries on standard Linux setups |
| Large disk support | Traditionally limited to about 2 TiB with 512-byte sectors | Supports much larger disks |
| Extended/logical partitions | Supported | Not needed |
| Backup partition metadata | No equivalent backup table | Backup metadata at the end of disk |
| Typical usage | Legacy compatibility | Modern systems |

### Primary, Extended and Logical Partitions

These terms apply to MBR partitioning.

- **Primary partition:** One of up to four MBR partition entries.
- **Extended partition:** A container that occupies one MBR entry and allows logical partitions inside it.
- **Logical partition:** A partition created inside an extended partition.

Example:

```text
/dev/sdb
├── /dev/sdb1    Primary
├── /dev/sdb2    Primary
├── /dev/sdb3    Extended
│   ├── /dev/sdb5    Logical
│   └── /dev/sdb6    Logical
```

Logical partition numbering traditionally starts at 5.

GPT does not use the primary/extended/logical partition arrangement.

## 4. Create a Partition Using fdisk

**Warning:** Use only a confirmed unused practice disk. Partitioning the wrong disk can destroy data.

Identify the disk:

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
sudo fdisk -l
```

Assume `/dev/sdb` is an unused practice disk.

Start fdisk:

```bash
sudo fdisk /dev/sdb
```

Common interactive commands:

```text
p       Print partition table
n       Create partition
Enter   Accept default partition number if prompted
Enter   Accept default first sector
+5G     Set partition size
p       Review partition table
w       Write changes and exit
```

Prompts differ depending on whether the disk uses MBR or GPT. If fdisk asks for a partition type, follow the prompt appropriate to your intended layout.

Verify:

```bash
lsblk
sudo fdisk -l /dev/sdb
```

## 5. Create a GPT Partition Using gdisk

`gdisk` is a tool for managing GPT partition tables.

```bash
sudo gdisk /dev/sdb
```

Common interactive commands:

```text
p       Print partition table
n       Create new partition
1       Partition number, if prompted
Enter   Accept default first sector
+5G     Set partition size
Enter   Accept default Linux filesystem type
p       Review partition table
w       Write changes and exit
```

Verify:

```bash
lsblk
sudo gdisk -l /dev/sdb
```

Do not convert or rewrite an existing disk's partition table unless you have confirmed the data and backup requirements.

## 6. Linux Filesystems: ext2, ext3 and ext4

A filesystem organizes files, directories, metadata and free space on a storage device.

| Feature | ext2 | ext3 | ext4 |
|---|---|---|---|
| Journaling | No | Yes | Yes |
| Crash recovery | Generally slower | Improved by journaling | Improved by journaling |
| Extents | No | No | Yes |
| Large filesystem support | More limited | Improved over ext2 | Much larger limits |
| Typical use | Legacy or specialized systems | Legacy systems | Common general-purpose Linux use |

### What is journaling?

Journaling records intended filesystem metadata changes so the filesystem can recover more quickly after an unexpected shutdown.

Journaling does not replace backups or guarantee that every recent application write survives a power failure.

### What are extents?

Ext4 can track contiguous ranges of blocks as extents. This can reduce metadata overhead and improve efficiency when working with large files.

For many general-purpose Linux workloads, ext4 is a sensible default unless the environment specifies XFS or another filesystem.

## 7. Create a Filesystem Using mkfs

After creating `/dev/sdb1`, create an ext4 filesystem:

```bash
sudo mkfs.ext4 /dev/sdb1
```

Verify:

```bash
lsblk -f
sudo blkid /dev/sdb1
```

Remember:

- `fdisk` or `gdisk` creates a partition.
- `mkfs.ext4` creates a filesystem.
- `mount` attaches a filesystem to a directory.

**Warning:** Formatting can destroy existing data. Confirm the target device before using `mkfs`.

## 8. Mount Storage in Linux

A mount point is the directory through which a filesystem is accessed.

Create a directory:

```bash
sudo mkdir -p /data
```

Mount the partition:

```bash
sudo mount /dev/sdb1 /data
```

Verify:

```bash
findmnt /data
df -hT /data
lsblk -f
```

Unmount:

```bash
sudo umount /data
```

If unmounting fails because the device is busy, inspect which processes are using it:

```bash
sudo fuser -vm /data
```

Linux uses one directory tree starting at `/`. Mounting makes a separate filesystem available through a directory such as `/data`, `/mnt/backup`, or `/var/log`.

## 9. Permanent Mounting Using /etc/fstab

A mount created manually with `mount` usually does not persist after reboot. The `/etc/fstab` file defines filesystems to mount automatically.

### Step 1: Find the filesystem UUID

```bash
sudo blkid /dev/sdb1
```

Example output:

```text
/dev/sdb1: UUID="1234-abcd-example" TYPE="ext4"
```

Use the real UUID from your machine.

### Step 2: Create the mount point

```bash
sudo mkdir -p /data
```

### Step 3: Edit /etc/fstab

```bash
sudo vi /etc/fstab
```

Add a line using the actual UUID:

```fstab
UUID=1234-abcd-example  /data  ext4  defaults  0  2
```

Meaning of the six fields:

1. `UUID=...` — identifies the filesystem.
2. `/data` — mount point.
3. `ext4` — filesystem type.
4. `defaults` — standard mount options.
5. `0` — legacy dump utility setting.
6. `2` — filesystem check order; root normally uses `1`.

### Step 4: Validate before rebooting

```bash
sudo findmnt --verify
sudo mount -a
findmnt /data
df -hT /data
```

Correct any reported errors before rebooting. An invalid `/etc/fstab` entry can cause boot delays or recovery problems.

**Best practice:** Use filesystem UUIDs instead of `/dev/sdb1`, because device names can change across boots.

## 10. DAS vs NAS vs SAN

### DAS — Direct Attached Storage

Storage directly attached to a server.

Examples:
- Local HDD or SSD.
- A virtual disk attached to a Linux VM.
- A directly attached storage device.

Advantages:
- Simple to configure.
- Often low latency.
- Suitable for individual servers.

Limitations:
- Sharing between multiple servers usually requires additional mechanisms.

### NAS — Network Attached Storage

A storage appliance that provides file-level access over a network.

Common protocols:
- NFS
- SMB/CIFS

Advantages:
- Shared directories for multiple servers.
- Centralized file management.
- Useful for shared files and backups.

Example: Multiple Linux servers accessing a shared NFS directory.

### SAN — Storage Area Network

A specialized network that provides block storage to servers.

Common technologies:
- Fibre Channel
- iSCSI

Advantages:
- Centralized enterprise storage.
- High availability and advanced storage management.
- Common in enterprise databases and virtualization.

Example: A SAN presents a LUN to Linux, which sees it as a block device.

### Comparison

| Feature | DAS | NAS | SAN |
|---|---|---|---|
| Access type | Block | File | Block |
| Connection | Direct/local | Ethernet network | Storage network |
| Common protocols | SATA, SAS, NVMe | NFS, SMB | Fibre Channel, iSCSI |
| Typical usage | Individual server disks | Shared files | Enterprise databases and virtualization |

**Interview tip:** NAS provides file-level access; SAN provides block-level access. A shared SAN block device must not be independently mounted read/write by multiple servers with an ordinary non-cluster-aware filesystem.

## 11. LVM — Logical Volume Manager

LVM provides a flexible storage layer between physical disks or partitions and filesystems.

Without LVM:

```text
Disk → Partition → Filesystem → Mount Point
```

With LVM:

```text
Disk/Partition
      |
      v
Physical Volume (PV)
      |
      v
Volume Group (VG)
      |
      v
Logical Volume (LV)
      |
      v
Filesystem
      |
      v
Mount Point
```

### LVM Components

- **PV — Physical Volume:** A disk or partition initialized for LVM.
- **VG — Volume Group:** A storage pool built from one or more PVs.
- **LV — Logical Volume:** A virtual block device allocated from a VG.
- **PE — Physical Extent:** The allocation unit used internally by LVM.

### Practical LVM Example

Assume `/dev/sdb` and `/dev/sdc` are unused practice disks.

Inspect them first:

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
```

Initialize physical volumes:

```bash
sudo pvcreate /dev/sdb /dev/sdc
```

Create a volume group:

```bash
sudo vgcreate vg_data /dev/sdb /dev/sdc
```

Create a 10 GiB logical volume:

```bash
sudo lvcreate -L 10G -n lv_appdata vg_data
```

Create an ext4 filesystem:

```bash
sudo mkfs.ext4 /dev/vg_data/lv_appdata
```

Create the mount point and mount:

```bash
sudo mkdir -p /appdata
sudo mount /dev/vg_data/lv_appdata /appdata
```

Verify:

```bash
sudo pvs
sudo vgs
sudo lvs
lsblk -f
df -hT /appdata
```

The logical volume is now accessible at `/appdata`.

**Warning:** `pvcreate` and `mkfs` modify storage metadata. Use only confirmed unused disks.

### Make the LVM Mount Permanent

Find the filesystem UUID:

```bash
sudo blkid /dev/vg_data/lv_appdata
```

Add the real UUID to `/etc/fstab`:

```fstab
UUID=<actual-filesystem-uuid>  /appdata  ext4  defaults  0  2
```

Validate:

```bash
sudo findmnt --verify
sudo mount -a
```

### Extend an LVM Volume

Check free space:

```bash
sudo vgs
sudo lvs
```

If the VG has at least 5 GiB free, extend the LV and grow its filesystem:

```bash
sudo lvextend -r -L +5G /dev/vg_data/lv_appdata
```

Verify:

```bash
sudo lvs
df -hT /appdata
```

The `-r` option requests filesystem resizing along with logical-volume growth when supported.

If the VG has no free space, add another suitable PV and extend the VG first. Shrinking logical volumes is more dangerous and requires filesystem-specific procedures.

## 12. Common Storage Troubleshooting

| Problem | Commands |
|---|---|
| Disk not visible | `lsblk`, `sudo fdisk -l` |
| Filesystem missing | `sudo blkid`, `lsblk -f` |
| Mount fails | `sudo mount -v /dev/sdb1 /data`, `dmesg` |
| Mount missing after reboot | `cat /etc/fstab`, `sudo findmnt --verify` |
| Disk full | `df -hT`, `sudo du -xhd1 /var` |
| Inodes exhausted | `df -i` |
| LVM has no free space | `sudo pvs`, `sudo vgs`, `sudo lvs` |
| Unmount says busy | `sudo fuser -vm /data` |

### Storage Best Practices

- Identify the correct disk before partitioning or formatting.
- Use UUIDs in `/etc/fstab`.
- Back up important data before storage changes.
- Validate mount configuration before rebooting.
- Monitor filesystem space, inode usage and latency.
- Do not shrink volumes or rewrite partition tables without a verified plan.

## 13. Linux Storage Interview Questions

### Q1. What is the difference between a disk, partition, filesystem and mount point?

A disk is the storage device. A partition is a division of the disk. A filesystem organizes files and metadata. A mount point is the directory where the filesystem is accessed.

### Q2. What is the difference between MBR and GPT?

MBR is the older partition scheme with four primary partition entries and an extended/logical partition workaround. GPT supports many partitions and keeps backup partition metadata.

### Q3. What does mkfs.ext4 do?

It creates an ext4 filesystem on a specified device. It does not create a partition or mount the filesystem.

### Q4. What is the difference between df and du?

`df` reports filesystem space usage. `du` reports space consumed by files and directories.

### Q5. Why use UUID in /etc/fstab?

UUIDs identify filesystems more reliably than device names such as `/dev/sdb1`, which may change across boots.

### Q6. What is the difference between DAS, NAS and SAN?

DAS is directly attached storage, NAS provides file-level access over a network, and SAN provides block-level storage over a storage network.

### Q7. Why use LVM?

LVM pools storage into volume groups and allocates logical volumes, making storage expansion and administration more flexible.

### Q8. How do you extend an LVM volume?

Check free VG space using `vgs`, then use `lvextend`. Grow the filesystem as well; `lvextend -r` can handle both for supported filesystems.

### Q9. How do you mount a filesystem permanently?

Find its UUID, create a mount point, add the correct entry to `/etc/fstab`, then validate with `findmnt --verify` and `mount -a`.

### Q10. What happens if /etc/fstab contains an invalid entry?

Mounting can fail during boot and may cause boot delays or recovery mode, depending on system configuration. Validate entries before rebooting.

## 14. Hands-on Practice Checklist

- [ ] Identify disks using `lsblk` and `fdisk -l`.
- [ ] Create a GPT partition using `fdisk` or `gdisk`.
- [ ] Create an ext4 filesystem.
- [ ] Mount the filesystem at `/data`.
- [ ] Configure a UUID-based `/etc/fstab` entry.
- [ ] Validate with `findmnt --verify` and `mount -a`.
- [ ] Compare `df -hT` and `du -sh`.
- [ ] Create PV, VG and LV using LVM.
- [ ] Extend an LV and grow its filesystem.
- [ ] Explain DAS, NAS and SAN in an interview.

## Summary

For Linux administration and DevOps interviews, focus on:

1. Disk and partition inspection.
2. MBR vs GPT and partition creation.
3. ext4 filesystem creation.
4. Mounting and persistent mounts with `/etc/fstab`.
5. Troubleshooting full disks and inode exhaustion.
6. LVM creation and expansion.
7. DAS, NAS and SAN differences.

These skills are useful in Linux administration, cloud support, AWS infrastructure operations and DevOps engineering.