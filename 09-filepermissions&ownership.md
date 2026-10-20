# Linux File Permissions and Ownership — Complete Notes

> **Learning objective:** Understand who can access a file or directory, what they can do, and how to change ownership and permissions safely in real-world Linux and DevOps environments.

## 1. Why does Linux need file permissions?

Linux uses file ownership and permissions to control access to files and directories.

For example, imagine a Linux server running a web application:

- A developer needs to edit application files.
- The web server needs to read application files.
- Other users should not be able to modify those files.
- A private configuration file containing credentials should not be readable by unauthorized users.

Linux controls access using three main permission classes:

1. **Owner** — the user who owns the file.
2. **Group** — users who belong to the file's group.
3. **Others** — everyone else.

Permissions determine what each class can do.

---

## 2. Understand the output of `ls -l`

Command:

```bash
ls -l
```

Example output:

```text
-rwxr-xr-- 1 bob developers 1024 Oct 20 09:00 deploy.sh
```

Breakdown:

| Field | Value | Meaning |
|---|---|---|
| 1 | `-rwxr-xr--` | File type and permissions |
| 2 | `1` | Hard-link count |
| 3 | `bob` | Owner |
| 4 | `developers` | Group |
| 5 | `1024` | File size in bytes |
| 6 | `Oct 20 09:00` | Modification time |
| 7 | `deploy.sh` | Filename |

The owner is `bob`, the group is `developers`, and the permission string is `rwxr-xr--`.

### File type identifiers

The first character of the permission string identifies the file type.

| Identifier | File type | Example |
|---|---|---|
| `-` | Regular file | `script.sh` |
| `d` | Directory | `/home/bob` |
| `l` | Symbolic link | `current -> release-1` |
| `c` | Character device | Terminal device |
| `b` | Block device | Disk device |
| `p` | Named pipe (FIFO) | Inter-process communication |
| `s` | Unix domain socket | Local service communication |

Examples:

```bash
ls -l
ls -ld /home/bob
file deploy.sh
```

- `ls -l` lists directory contents with details.
- `ls -ld` shows details about the directory itself.
- `file` identifies the type of a specified file.

**Important:** A filename extension such as `.sh` does not determine Linux file permissions or necessarily prove the file's type.

---

## 3. Owner, group and others

Linux divides the nine standard permission bits into three groups.

```text
-rwxr-xr--
 ||| ||| |||
 OOO GGG TTT

O = Owner
G = Group
T = Others
```

For example:

```text
-rwxr-xr--
```

- `-` — regular file
- `rwx` — owner can read, write and execute
- `r-x` — group can read and execute
- `r--` — others can only read

### How Linux determines which permissions apply

When a user accesses a file, Linux checks the applicable identity and permissions, including the owner, group memberships, ACLs and other security controls where relevant.

Do not assume that permissions from different classes are added together. For a normal file access check, the matching owner class takes precedence over the group class, which takes precedence over others.

---

## 4. Meaning of `r`, `w` and `x`

### Permissions on regular files

| Permission | Meaning | Example |
|---|---|---|
| `r` — Read | Read file contents | View a text file |
| `w` — Write | Modify file contents | Edit a configuration |
| `x` — Execute | Execute the file as a program, subject to other requirements | Run a script |

### Permissions on directories

Directory permissions have different practical meanings.

| Permission | Meaning | Example |
|---|---|---|
| `r` — Read | List directory entries | Run `ls` |
| `w` — Write | Create, delete or rename entries, subject to other restrictions | Add or remove files |
| `x` — Execute/search | Traverse the directory and access entries when permitted | `cd` into a directory |

**Important:** A directory usually needs `x` to access files inside it. Read permission alone may allow listing names but not opening the entries.

Deleting a file is primarily controlled by the permissions on its parent directory, not simply by whether the file itself is writable. Sticky-bit and ACL rules can also affect the result.

---

## 5. Symbolic permission mode

Symbolic mode uses letters to specify the permission class and the change to apply.

### Permission classes

| Symbol | Meaning |
|---|---|
| `u` | User/owner |
| `g` | Group |
| `o` | Others |
| `a` | All classes |

### Operators

| Operator | Meaning |
|---|---|
| `+` | Add permissions |
| `-` | Remove permissions |
| `=` | Set exactly the specified permissions for that class |

### Examples

Add execute permission for the owner:

```bash
chmod u+x deploy.sh
```

Remove write permission from others:

```bash
chmod o-w report.txt
```

Give the group read permission:

```bash
chmod g+r report.txt
```

Set owner permissions to read and write only:

```bash
chmod u=rw report.txt
```

Remove all permissions from others:

```bash
chmod o= report.txt
```

Give everyone read permission:

```bash
chmod a+r report.txt
```

Set owner read/write, group read, and others no access:

```bash
chmod u=rw,g=r,o= secret.txt
```

Verify:

```bash
ls -l secret.txt
```

Expected permission pattern:

```text
-rw-r----- 
```

---

## 6. Numeric (octal) permission mode

Numeric mode represents each permission with a number:

| Permission | Value |
|---|---:|
| Read (`r`) | 4 |
| Write (`w`) | 2 |
| Execute (`x`) | 1 |
| No permission | 0 |

Add the values to calculate the number for each class.

### Common combinations

| Numeric value | Permissions |
|---:|---|
| `0` | `---` |
| `1` | `--x` |
| `2` | `-w-` |
| `3` | `-wx` |
| `4` | `r--` |
| `5` | `r-x` |
| `6` | `rw-` |
| `7` | `rwx` |

### How to calculate a permission mode

For example, `754`:

- Owner: `7 = 4 + 2 + 1 = rwx`
- Group: `5 = 4 + 1 = r-x`
- Others: `4 = r--`

Therefore:

```bash
chmod 754 deploy.sh
```

Expected output:

```text
-rwxr-xr--
```

### Common numeric modes

| Mode | Result | Typical use |
|---|---|---|
| `600` | Owner read/write only | Private files |
| `640` | Owner read/write; group read | Group-readable configuration |
| `644` | Owner read/write; everyone else read | Ordinary text files |
| `700` | Owner full access only | Private directories or scripts |
| `750` | Owner full; group read/execute | Restricted shared directories or scripts |
| `755` | Owner full; others read/execute | Publicly readable programs and directories |
| `770` | Owner and group full access | Controlled team workspace |
| `775` | Owner/group full; others read/execute | Shared directories where appropriate |
| `777` | Everyone full access | Generally avoid |

These are common patterns, not universal requirements. Choose permissions according to the application's needs.

**Security rule:** Avoid `chmod 777` as a troubleshooting shortcut. It grants broad access and can create security problems.

---

## 7. Change permissions with `chmod`

`chmod` means **change mode**. It changes permission bits, not file ownership.

### Change a file's permissions

```bash
chmod 640 report.txt
```

### Make a script executable

```bash
chmod u+x deploy.sh
```

Run it:

```bash
./deploy.sh
```

The script also needs a valid interpreter or executable format and sufficient access to its path.

### Change permissions recursively

```bash
chmod -R 750 /opt/myapp
```

Use `-R` carefully. It applies permissions to all files and subdirectories and may give regular files execute permission unnecessarily.

For a mixed directory tree, set directory and file permissions separately when appropriate:

```bash
find /opt/myapp -type d -exec chmod 750 {} +
find /opt/myapp -type f -exec chmod 640 {} +
```

Only use this example if those permissions fit the application; executable scripts may need execute permission.

### Verify permissions

```bash
ls -l report.txt
stat report.txt
```

---

## 8. Change ownership with `chown`

`chown` means **change owner**.

### Change only the owner

```bash
sudo chown mercury report.txt
```

The file's group remains unchanged.

### Change both owner and group

```bash
sudo chown mercury:developers report.txt
```

### Change only the group using `chown`

```bash
sudo chown :developers report.txt
```

### Change ownership recursively

```bash
sudo chown -R mercury:developers /opt/myapp
```

**Caution:** Recursive changes affect everything below the specified path. Verify the path before executing the command.

### Verify the result

```bash
ls -l report.txt
stat report.txt
```

Example:

```text
-rw-r----- 1 mercury developers 1024 Oct 20 09:00 report.txt
```

Here, `mercury` owns the file and `developers` is its group.

### What if the owner name does not appear?

Check that the account exists:

```bash
id mercury
getent passwd mercury
```

Check the numeric IDs:

```bash
ls -ln report.txt
```

`ls -ln` displays numeric UID/GID values instead of names. Linux resolves those numbers through its account databases when displaying names.

If `chown` reports `Operation not permitted`, check the command's privileges, the target filesystem, any container or mount restrictions, and whether the operation is permitted by the system.

---

## 9. Change group ownership with `chgrp`

`chgrp` means **change group**.

Change a file's group:

```bash
sudo chgrp developers report.txt
```

Change the group of a directory tree:

```bash
sudo chgrp -R developers /opt/myapp
```

Verify:

```bash
ls -l report.txt
```

### Difference between `chmod`, `chown` and `chgrp`

| Command | Changes | Example |
|---|---|---|
| `chmod` | Permission bits | `chmod 640 report.txt` |
| `chown` | Owner, optionally group | `chown mercury:developers report.txt` |
| `chgrp` | Group ownership | `chgrp developers report.txt` |

---

## 10. Real-world DevOps scenario: Web application files

Imagine a web application is deployed at:

```text
/opt/myapp
```

The directory contains application files, configuration, and scripts.

### Scenario A: A developer cannot edit a file

Check ownership and permissions:

```bash
ls -l /opt/myapp/config.yml
id
```

If the file should belong to the deployment user and group, an administrator may correct it:

```bash
sudo chown deploy:developers /opt/myapp/config.yml
sudo chmod 640 /opt/myapp/config.yml
```

This permits the owner to read/write and the group to read. Verify that the application and deployment process need exactly those permissions before applying them.

### Scenario B: A deployment script says `Permission denied`

Inspect the file:

```bash
ls -l deploy.sh
```

If the owner should be allowed to execute it:

```bash
chmod u+x deploy.sh
```

Run:

```bash
./deploy.sh
```

If it still fails, inspect the script's interpreter, parent-directory search permissions, mount options such as `noexec`, and any security policy restrictions.

### Scenario C: A web server cannot read application files

Identify the service account:

```bash
ps -eo user,group,comm | grep -E 'nginx|httpd|apache2'
```

Check the path and its permissions:

```bash
namei -l /opt/myapp/index.html
```

The web-server account needs appropriate access to every parent directory and the file. Correct the ownership, group membership or permissions according to the deployment design rather than making everything world-writable.

### Scenario D: A team needs to share deployment files

Create or reuse a dedicated group, add the required users, and configure the shared directory deliberately.

For example:

```bash
sudo groupadd developers
sudo usermod -aG developers deploy
sudo chown -R deploy:developers /opt/myapp
sudo chmod -R 750 /opt/myapp
```

This is only an illustrative setup. It assumes that `deploy` exists, that `developers` is appropriate, and that all files are meant to receive the same permissions. In a real application, distinguish directories, data files, secrets and executable scripts.

---

## 11. Directory setgid and default group inheritance

For a shared team directory, the setgid bit can make new entries inherit the directory's group in typical Linux configurations.

```bash
sudo chown deploy:developers /opt/myapp
sudo chmod 2770 /opt/myapp
```

The leading `2` sets the directory's setgid bit.

Verify:

```bash
ls -ld /opt/myapp
```

Example permission string:

```text
drwxrws--- 
```

For more predictable shared group collaboration, administrators can also configure a default ACL or a suitable umask.

The setgid bit does not itself guarantee group write access for every newly created file.

---

## 12. Special permission bits

In addition to the standard nine permission bits, Linux supports special bits.

| Bit | Numeric value | Common purpose |
|---|---:|---|
| setuid | `4` in the leading position | A program may run with its file owner's effective UID |
| setgid | `2` in the leading position | A program may run with its file group's effective GID; on directories, new entries commonly inherit the directory's group |
| sticky bit | `1` in the leading position | On shared directories, restricts who can remove or rename entries |

Examples:

```bash
chmod 4755 program
chmod 2770 shared/
chmod 1777 /tmp/example
```

Special bits can affect security and should be configured only when needed.

---

## 13. Practical troubleshooting checklist

When a user cannot access a file, follow this order:

1. **Confirm the path**
   ```bash
   ls -ld /path/to/parent /path/to/file
   ```

2. **Check ownership and permissions**
   ```bash
   ls -l /path/to/file
   stat /path/to/file
   ```

3. **Check the user's identity and groups**
   ```bash
   id username
   ```

4. **Inspect every directory in the path**
   ```bash
   namei -l /path/to/file
   ```

5. **Check ACLs if applicable**
   ```bash
   getfacl /path/to/file
   ```

6. **Change only what is necessary**
   ```bash
   sudo chown username:groupname /path/to/file
   sudo chmod 640 /path/to/file
   ```

7. **Verify again**
   ```bash
   ls -l /path/to/file
   ```

Do not assume every `Permission denied` error is caused by `chmod`. The cause may be ownership, parent-directory permissions, ACLs, a read-only filesystem, a `noexec` mount, SELinux, or another security control.

---

## 14. Quick revision

- `ls -l` — view file type, permissions, owner and group.
- `ls -ld directory/` — inspect a directory itself.
- `chmod` — change permissions.
- `chown` — change owner and optionally group.
- `chgrp` — change group.
- `stat` — inspect file metadata.
- `id` — check user UID/GID and groups.
- `namei -l` — inspect permissions along a path.
- `getfacl` — inspect ACLs where supported.
- `chmod 640 file` — owner read/write, group read, others no access.
- `chmod u+x script.sh` — add execute permission for the owner.
- `chown user:group file` — change owner and group.

### The key difference to remember

**Permissions answer:** “What can this user do?”

**Ownership answers:** “Who owns this file, and which group is associated with it?”

In DevOps, both matter: correct ownership without suitable permissions may still block access, and permissive permissions do not automatically make the ownership correct.
