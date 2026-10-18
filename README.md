# linux-basics
Linux fundamentals and hands-on labs using KodeKloud. Practical notes on shell commands, Linux architecture, package management, networking, security, permissions, systemd, and storage for DevOps, Cloud Engineering, and SRE preparation.
# Linux Basics — Learning Notes & Hands-on Labs

A structured Linux learning repository documenting my Linux fundamentals, KodeKloud course progress, practical command-line exercises, troubleshooting, and interview preparation for **DevOps Engineering, Cloud Engineering, and Site Reliability Engineering (SRE)**.

## 🎯 Learning Objectives

- Understand Linux operating system fundamentals and architecture.
- Become comfortable working with the Linux command line and Bash shell.
- Manage files, directories, users, groups, and permissions.
- Install and manage software packages.
- Understand Linux networking and basic troubleshooting.
- Manage services and processes using systemd and related tools.
- Understand Linux storage, filesystems, and disk usage.
- Practice security fundamentals and system administration.
- Document hands-on labs, errors, troubleshooting steps, and solutions.
- Build a strong foundation for Docker, Kubernetes, and cloud infrastructure.

## 📚 Course Roadmap

Course: KodeKloud — Learning Linux Basics Course & Labs

| No. | Module | Topics to Document | Status |
|---|---|---|---|
| 01 | Introduction | Linux overview, distributions, architecture, terminal basics | In progress |
| 02 | Working with Shell - I | Basic commands, files, directories, command syntax | Pending |
| 03 | Linux Core Concepts | Filesystem hierarchy, processes, users, environment | Pending |
| 04 | Package Management | Package installation, updates, removal, repositories | Pending |
| 05 | Working with Shell - II | Advanced command-line operations, text processing, pipes | Pending |
| 06 | Networking | IP addresses, interfaces, connectivity, DNS, ports | Pending |
| 07 | Security and File Permissions | Ownership, permissions, users, groups, access control | Pending |
| 08 | Service Management with systemd | Services, units, systemctl, service troubleshooting | Pending |
| 09 | Storage in Linux | Disks, partitions, filesystems, mounting, disk usage | Pending |
| 10 | The Client Demonstration | Review, practical exercises, and troubleshooting | Pending |

*Progress note: The screenshot shows 7% overall course completion and the Introduction module marked complete. Update the table as you finish each module.*

## 🗂️ Repository Structure

```text
linux-basics/
├── README.md
├── notes/
│   ├── 01-linux-introduction.md
│   ├── 02-working-with-shell-1.md
│   ├── 03-linux-core-concepts.md
│   ├── 04-package-management.md
│   ├── 05-working-with-shell-2.md
│   ├── 06-linux-networking.md
│   ├── 07-security-file-permissions.md
│   ├── 08-systemd-service-management.md
│   ├── 09-linux-storage.md
│   └── 10-client-demonstration.md
├── labs/
│   ├── 01-basic-linux-commands.md
│   ├── 02-users-and-permissions.md
│   ├── 03-network-troubleshooting.md
│   ├── 04-service-management.md
│   └── 05-storage-management.md
├── scripts/
│   └── README.md
└── learning-logs/
    └── learning-log.md
```

## 🐧 Linux Fundamentals

### What is Linux?

Linux is an open-source, Unix-like operating system kernel. Linux distributions combine the kernel with system utilities, package managers, libraries, and other software to provide a complete operating system.

Common distributions include Ubuntu, Debian, Fedora, Red Hat Enterprise Linux, Rocky Linux, and AlmaLinux.

### Why Linux is important in DevOps

- Most cloud servers and many container workloads run Linux.
- Docker containers commonly use Linux kernel features.
- Kubernetes nodes frequently run Linux.
- Linux provides tools for automation, monitoring, networking, and troubleshooting.
- SRE engineers use Linux to diagnose performance issues, manage services, and investigate incidents.

### Linux architecture

```text
+----------------------------------+
| Applications and User Commands   |
+----------------------------------+
| Shell and System Utilities       |
+----------------------------------+
| System Libraries                 |
+----------------------------------+
| Linux Kernel                     |
+----------------------------------+
| Hardware                         |
+----------------------------------+
```

- **Hardware:** CPU, memory, disks, and network devices.
- **Kernel:** Manages processes, memory, devices, filesystems, and networking.
- **System libraries:** Provide interfaces that applications use to interact with the kernel.
- **Shell:** Interprets commands and supports scripting.
- **Utilities and applications:** Provide system administration and application functionality.

### What is a Linux shell?

A shell provides an interface for executing commands and scripts.

Common shells include Bash, Zsh, and Dash.

Check your current shell:

```bash
echo "$SHELL"
```

Check your current user:

```bash
whoami
```

Display the current directory:

```bash
pwd
```

## 🧰 Essential Linux Commands

### Navigation

| Command | Purpose |
|---|---|
| `pwd` | Display the current working directory |
| `ls` | List files and directories |
| `ls -l` | Show detailed file information |
| `ls -la` | Include hidden files |
| `cd /path` | Change directory |
| `cd ..` | Move to the parent directory |
| `cd ~` | Move to the user's home directory |

### File and directory management

| Command | Purpose |
|---|---|
| `touch file.txt` | Create an empty file if it does not exist |
| `mkdir project` | Create a directory |
| `mkdir -p a/b/c` | Create nested directories |
| `cp source destination` | Copy a file |
| `cp -r source destination` | Copy a directory recursively |
| `mv old new` | Move or rename a file |
| `rm file.txt` | Remove a file |
| `rmdir directory` | Remove an empty directory |
| `find /path -name "*.log"` | Find files by name |

**Caution:** `rm -r` and especially `rm -rf` can permanently delete files. Check the target path before running them.

### Reading files

| Command | Purpose |
|---|---|
| `cat file.txt` | Display a file |
| `less file.txt` | Read a file interactively |
| `head file.txt` | Display the first 10 lines |
| `head -n 20 file.txt` | Display the first 20 lines |
| `tail file.txt` | Display the last 10 lines |
| `tail -f app.log` | Follow a growing log file |
| `wc -l file.txt` | Count lines |
| `file filename` | Identify file type |

### Searching and text processing

```bash
grep "error" app.log
grep -i "error" app.log
grep -n "error" app.log
grep -r "TODO" .
sort names.txt
uniq names.txt
cut -d: -f1 /etc/passwd
```

- `grep`: Search for matching text.
- `sort`: Sort lines.
- `uniq`: Remove adjacent duplicate lines.
- `cut`: Extract fields from text.
- `wc`: Count lines, words, and bytes.

To remove repeated adjacent lines after sorting:

```bash
sort names.txt | uniq
```

### Pipes and redirection

```bash
command1 | command2
```

A pipe sends the standard output of one command to the standard input of another.

```bash
ls > files.txt
echo "Linux practice" >> notes.txt
command > output.txt 2> errors.txt
command &> combined.log
```

- `>` overwrites a file.
- `>>` appends to a file.
- `2>` redirects standard error.
- `&>` redirects standard output and standard error in Bash.

## 🗃️ Linux Filesystem Hierarchy

| Directory | Purpose |
|---|---|
| `/` | Root of the filesystem |
| `/home` | Home directories for regular users |
| `/root` | Root user's home directory |
| `/etc` | System-wide configuration files |
| `/var` | Variable data, logs, caches, and spool files |
| `/var/log` | Common location for system and application logs |
| `/tmp` | Temporary files |
| `/usr` | Many user applications, libraries, and shared resources |
| `/bin` | Essential command binaries on traditional layouts; often linked to `/usr/bin` |
| `/sbin` | System administration binaries on traditional layouts; often linked to `/usr/sbin` |
| `/dev` | Device files |
| `/proc` | Virtual filesystem exposing process and kernel information |
| `/sys` | Kernel and device information |
| `/boot` | Files needed to boot the system |
| `/mnt` | Common temporary mount point |
| `/media` | Common mount location for removable media |

Important distinction:

- `/` is the filesystem root.
- `/root` is the root user's home directory.
- `/home/username` is a typical regular user's home directory.

## 📦 Package Management

Package managers install, update, and remove software.

### Debian and Ubuntu

```bash
sudo apt update
sudo apt upgrade
sudo apt install nginx
sudo apt remove nginx
apt search nginx
dpkg -l
```

`apt update` refreshes package indexes; it does not itself upgrade installed packages.

### RHEL, Rocky Linux, and AlmaLinux

```bash
sudo dnf check-update
sudo dnf upgrade
sudo dnf install nginx
sudo dnf remove nginx
dnf search nginx
rpm -qa
```

Older systems may use `yum`.

Package management differs by distribution. Check which operating system you are using before choosing commands:

```bash
cat /etc/os-release
```

## 🌐 Linux Networking

### Essential networking commands

```bash
ip addr
ip link
ip route
hostname
getent hosts example.com
ping -c 4 8.8.8.8
ss -tulpn
```

| Command | Purpose |
|---|---|
| `ip addr` | Display IP addresses |
| `ip link` | Display network interfaces and their state |
| `ip route` | Display routing information |
| `hostname` | Display the hostname |
| `getent hosts` | Resolve a hostname using configured name services |
| `ping` | Test ICMP reachability where permitted |
| `ss -tulpn` | Inspect listening TCP/UDP sockets and processes where permissions allow |

### Troubleshooting checklist

1. Check whether the network interface is up.
2. Verify the IP address and subnet.
3. Inspect the default route.
4. Test connectivity to a reachable IP address.
5. Test DNS resolution.
6. Check the destination service, listening port, firewall, and routing rules.

An unsuccessful ping does not always mean a server is down: ICMP traffic may be blocked.

## 🔐 Linux Users, Groups, and Permissions

### Identity and account commands

```bash
whoami
id
who
groups
cat /etc/passwd
cat /etc/group
```

### File ownership

```bash
ls -l file.txt
sudo chown user:group file.txt
sudo chown -R user:group directory/
```

### Permission basics

Example:

```text
-rwxr-xr--
```

- First character: file type.
- Next three characters: owner permissions.
- Next three characters: group permissions.
- Final three characters: permissions for others.

| Permission | Meaning | Numeric value |
|---|---|---|
| Read (`r`) | Read file contents; for directories, list entries | 4 |
| Write (`w`) | Modify file contents; for directories, create/remove entries subject to other rules | 2 |
| Execute (`x`) | Execute a file; for directories, traverse/search | 1 |

### Using chmod

```bash
chmod 644 file.txt
chmod 755 script.sh
chmod u+x script.sh
```

- `644`: Owner can read/write; group and others can read.
- `755`: Owner can read/write/execute; group and others can read/execute.

### Users and groups

```bash
sudo useradd -m devuser
sudo passwd devuser
sudo groupadd developers
sudo usermod -aG developers devuser
id devuser
```

Use `sudo` only when required and permitted. Group membership changes may require a new login session to take effect.

## ⚙️ Process and Service Management

### Process commands

```bash
ps aux
top
pgrep nginx
kill PID
```

Replace `PID` with the actual process ID. `kill` sends a termination signal by default; use stronger signals only when necessary.

### systemd service management

```bash
systemctl status nginx
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl enable nginx
sudo systemctl disable nginx
systemctl list-units --type=service
```

- `start`: Start a service now.
- `stop`: Stop a service.
- `restart`: Restart a service.
- `enable`: Configure a service to start automatically at boot.
- `status`: Inspect the service's state and recent information.

### Logs

```bash
journalctl
journalctl -u nginx
journalctl -u nginx -n 50
journalctl -u nginx -f
```

Use logs and service status together when troubleshooting failures.

## 💽 Linux Storage

### Inspect disks and filesystems

```bash
lsblk
df -h
df -i
du -sh /var/log
findmnt
blkid
```

- `lsblk`: Show block devices and their relationships.
- `df -h`: Show filesystem space usage in human-readable units.
- `df -i`: Show inode usage.
- `du -sh`: Summarize disk usage for a path.
- `findmnt`: Display mounted filesystems.
- `blkid`: Display filesystem identification information.

### Mounting filesystems

```bash
sudo mkdir -p /mnt/data
sudo mount /dev/sdX1 /mnt/data
findmnt /mnt/data
sudo umount /mnt/data
```

`/dev/sdX1` is a placeholder, not a device to copy blindly. Identify the correct device and filesystem before mounting. Incorrect storage operations can cause data loss.

## 🧪 Hands-on Lab Checklist

- [ ] Identify the Linux distribution and kernel.
- [ ] Navigate directories and create, copy, move, and remove test files.
- [ ] Read files and search for specific text.
- [ ] Practice pipes and output redirection.
- [ ] Explore important directories under `/`.
- [ ] Install and inspect a package using the distribution's package manager.
- [ ] Inspect network interfaces, IP addresses, routes, and DNS.
- [ ] Create a test user and group.
- [ ] Change file permissions and ownership.
- [ ] Inspect processes and manage a test service.
- [ ] Read system and service logs.
- [ ] Inspect disks, filesystems, mounts, and free space.

Perform labs in a disposable VM or another environment where you have permission to make changes.

## 📝 Learning Log

### Day 1 — Linux Course Setup and Introduction

**Course:** KodeKloud — Learning Linux Basics Course & Labs

**Progress recorded:** 7% complete, as shown in the course screenshot.

**Learning goals:**
- Understand the purpose of Linux in IT infrastructure.
- Learn the Linux distribution and kernel concepts.
- Become familiar with the terminal and shell.
- Begin maintaining command notes and practical lab records.

**Commands practiced:** Update this list after executing each command.

```bash
pwd
whoami
hostname
uname -a
cat /etc/os-release
ls -la
```

**Doubts and troubleshooting:** Record the question, error message, root cause, and solution here.

**Next session:** Continue the course lessons and update the corresponding module notes.

## 💼 Interview Preparation Questions

1. What is Linux, and how is it different from a Linux distribution?
2. What is the difference between the kernel and the shell?
3. What is the purpose of `/etc`, `/var`, `/home`, and `/proc`?
4. What is the difference between an absolute path and a relative path?
5. What is the difference between `>` and `>>`?
6. How do pipes work in Linux?
7. What do `chmod 644` and `chmod 755` mean?
8. What is the difference between `chown` and `chmod`?
9. What is the difference between `apt` and `dnf`?
10. How would you troubleshoot a service that fails to start?
11. What is the difference between `df` and `du`?
12. How would you troubleshoot a server that cannot resolve a domain name?
13. What is the difference between `systemctl start` and `systemctl enable`?
14. How would you investigate a Linux server running out of disk space?
15. Why are Linux fundamentals important for Docker and Kubernetes?

## 📈 Progress Tracking

Update the module status only after completing the relevant course material and practical exercises.

| Metric | Current record |
|---|---|
| Course | Learning Linux Basics Course & Labs |
| Modules | 10 |
| Overall completion | 7% (screenshot) |
| Completed modules | Introduction |
| Current focus | Linux fundamentals and shell practice |
| Next milestone | Complete and document Working with Shell - I |

## 🚀 Future Improvements

As I progress, this repository will include:

- Detailed module-specific notes based on lessons completed.
- Real command outputs and lab screenshots.
- Troubleshooting records with root causes and solutions.
- Bash scripts for routine administration.
- Linux administration exercises and interview questions.
- Connections between Linux concepts and real-world DevOps/SRE workflows.

**Learning principle:** Understand the concept, run the command, observe the output, document the result, and repeat the exercise until it can be performed independently.
