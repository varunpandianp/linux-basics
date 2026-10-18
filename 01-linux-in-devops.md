# Linux & Linux in DevOps — Complete Study Notes

> **Learning path:** Linux Fundamentals → Linux Administration → Networking → Shell Scripting → DevOps → Cloud Engineering → SRE
>
> **Purpose:** Build a strong understanding of Linux concepts, practice commands in a Linux VM, troubleshoot common problems, and understand how Linux supports real-world DevOps infrastructure.

---

# Table of Contents

1. [What Is Linux?](#1-what-is-linux)
2. [Why Linux Is Important in DevOps](#2-why-linux-is-important-in-devops)
3. [Linux vs Windows](#3-linux-vs-windows)
4. [Linux Architecture](#4-linux-architecture)
5. [Linux Distributions](#5-linux-distributions)
6. [Linux Boot Process](#6-linux-boot-process)
7. [Shell and Terminal](#7-shell-and-terminal)
8. [Essential Linux Commands](#8-essential-linux-commands)
9. [Linux Filesystem Hierarchy](#9-linux-filesystem-hierarchy)
10. [File and Directory Management](#10-file-and-directory-management)
11. [Users, Groups, and Permissions](#11-users-groups-and-permissions)
12. [Processes and Process Management](#12-processes-and-process-management)
13. [Package Management](#13-package-management)
14. [Linux Networking](#14-linux-networking)
15. [Services and systemd](#15-services-and-systemd)
16. [Logs and Troubleshooting](#16-logs-and-troubleshooting)
17. [Linux Storage Management](#17-linux-storage-management)
18. [Environment Variables](#18-environment-variables)
19. [Bash Scripting Basics](#19-bash-scripting-basics)
20. [Linux Security](#20-linux-security)
21. [Linux in DevOps](#21-linux-in-devops)
22. [Linux in Docker and Kubernetes](#22-linux-in-docker-and-kubernetes)
23. [Linux in Cloud Engineering and SRE](#23-linux-in-cloud-engineering-and-sre)
24. [Real-World Troubleshooting Scenarios](#24-real-world-troubleshooting-scenarios)
25. [Hands-on Practice Labs](#25-hands-on-practice-labs)
26. [Common Linux Commands Cheat Sheet](#26-common-linux-commands-cheat-sheet)
27. [Interview Questions](#27-interview-questions)
28. [Learning Log](#28-learning-log)

---

# 1. What Is Linux?

Linux is an open-source, Unix-like operating system kernel. A complete Linux operating system is typically provided through a Linux distribution containing the kernel, system utilities, libraries, package management tools, and applications.

Linux was originally created by Linus Torvalds in 1991.

## 1.1 What is an operating system?

An operating system acts as an interface between applications and computer hardware.

Its responsibilities include:

- Managing CPU resources.
- Managing RAM and virtual memory.
- Managing processes and threads.
- Managing files and directories.
- Controlling hardware devices.
- Managing network communication.
- Enforcing permissions and security.
- Providing services required by applications.

## 1.2 Why do we need Linux?

Linux is widely used because it offers:

- Open-source software and flexible customization.
- Stability for server workloads.
- Powerful command-line administration.
- Automation through shell scripting.
- Fine-grained permissions and security controls.
- Support for cloud infrastructure and containers.
- A broad ecosystem of developer and infrastructure tools.

## 1.3 Where is Linux used?

| Area | Example |
|---|---|
| Servers | Web servers and application servers |
| Cloud | AWS EC2, Azure Linux VMs, Google Cloud VMs |
| Containers | Docker container workloads |
| Orchestration | Kubernetes worker and control-plane nodes |
| Networking | Routers, gateways, and network appliances |
| Embedded systems | IoT devices and appliances |
| Automation | Bash scripts and configuration management |
| Monitoring | Prometheus exporters and monitoring agents |
| SRE | Incident investigation and performance troubleshooting |

**Important:** Linux is not the same thing as a Linux distribution. Linux refers to the kernel; Ubuntu, Debian, and Rocky Linux are complete distributions.

---

# 2. Why Linux Is Important in DevOps

DevOps combines software development and IT operations through automation, collaboration, continuous delivery, and reliable infrastructure management.

Linux is a fundamental skill because many DevOps tools and production workloads run on Linux.

## 2.1 Linux responsibilities in DevOps

A DevOps engineer may use Linux to:

1. Provision cloud virtual machines.
2. Install and configure applications.
3. Manage users, groups, and access permissions.
4. Configure networking and firewall rules.
5. Run Docker containers.
6. Operate Kubernetes nodes.
7. Automate tasks using Bash and Python.
8. Investigate application and system logs.
9. Manage services with systemd.
10. Monitor CPU, memory, disk, and network usage.
11. Troubleshoot failed deployments.
12. Maintain secure and reliable systems.

## 2.2 Linux and the DevOps toolchain

| DevOps area | Tools or technologies | Linux knowledge required |
|---|---|---|
| Version control | Git | Files, permissions, SSH, shell |
| CI/CD | Jenkins, GitHub Actions, GitLab CI | Shell commands, processes, environment variables |
| Containers | Docker, containerd | Processes, namespaces, cgroups, networking |
| Orchestration | Kubernetes | Networking, processes, storage, services |
| Infrastructure as Code | Terraform | CLI, SSH, environment variables, cloud VMs |
| Configuration management | Ansible | SSH, users, permissions, packages, services |
| Monitoring | Prometheus, Grafana | Metrics, logs, networking, system resources |
| Cloud | AWS, Azure, GCP | Linux administration and troubleshooting |
| Scripting | Bash, Python | Files, commands, variables, exit codes |
| Reliability | SRE practices | Observability, resource management, incident response |

## 2.3 Example: Application deployment

Imagine deploying a web application on a Linux server.

```text
Developer pushes code
        |
        v
Git repository
        |
        v
CI/CD pipeline
        |
        v
Build application or container image
        |
        v
Deploy to Linux server or Kubernetes
        |
        v
Application starts
        |
        v
Monitor logs, metrics, and availability
```

Linux supports the operating environment at each stage: commands run in pipelines, services start on servers, containers use kernel features, and monitoring tools inspect resource usage.

## 2.4 What should a DevOps engineer learn first?

Recommended order:

1. Linux fundamentals and command-line navigation.
2. Files, directories, permissions, and ownership.
3. Users, groups, processes, and services.
4. Networking, DNS, IP addresses, and ports.
5. Package management and software installation.
6. Logs, troubleshooting, and storage.
7. Bash scripting and automation.
8. Git and CI/CD fundamentals.
9. Docker and container networking.
10. Cloud infrastructure and Kubernetes.

---

# 3. Linux vs Windows

| Feature | Linux | Windows |
|---|---|---|
| Kernel | Linux kernel | Windows NT kernel |
| Command line | Bash, Zsh, other shells | PowerShell, Command Prompt |
| Filesystem paths | Commonly `/home/user/file` | Commonly `C:\Users\user\file` |
| Filesystem root | `/` | Drive-based paths are common |
| Permissions | Owner, group, others, ACLs | ACL-based security model |
| Software installation | Distribution package managers and other methods | Installers, package managers, Microsoft Store |
| Automation | Bash, Python, Ansible | PowerShell, Python, other tools |
| Common server usage | Cloud servers, containers, web infrastructure | Windows applications, Active Directory, IIS |
| Case sensitivity | Common Linux filesystems are case-sensitive | Typical Windows filesystems are case-preserving but case-insensitive by default |

Linux and Windows can both support enterprise applications and automation. The appropriate platform depends on the workload.

---

# 4. Linux Architecture

```text
+---------------------------------------+
| User Applications                     |
| Nginx, Docker CLI, Git, Scripts       |
+---------------------------------------+
| Shell and System Utilities             |
| Bash, ls, grep, systemctl             |
+---------------------------------------+
| System Libraries                       |
| libc and other shared libraries        |
+---------------------------------------+
| Linux Kernel                           |
| Processes, Memory, Filesystems, Network|
+---------------------------------------+
| Hardware                               |
| CPU, RAM, Disks, Network Interfaces    |
+---------------------------------------+
```

## 4.1 Hardware

The physical or virtual resources used by the operating system:

- CPU.
- RAM.
- Storage devices.
- Network interfaces.
- Other hardware devices.

## 4.2 Kernel

The kernel is the core of the operating system.

Its responsibilities include:

- Process scheduling.
- Memory management.
- Device management.
- Filesystem operations.
- Networking.
- Security enforcement.
- System call interfaces.

Inspect the kernel version:

```bash
uname -r
uname -a
```

## 4.3 System libraries

Libraries provide functions that applications can use to access operating system capabilities.

For example, applications commonly use libraries that provide interfaces to kernel system calls.

## 4.4 Shell

A shell accepts commands and executes programs.

Examples:

- Bash.
- Zsh.
- Dash.
- Fish.

## 4.5 Applications and utilities

Examples include:

- `ls` for listing directory contents.
- `grep` for searching text.
- `ps` for inspecting processes.
- `systemctl` for managing systemd services.
- `ip` for inspecting network interfaces and routes.

---

# 5. Linux Distributions

A Linux distribution packages the Linux kernel with user-space tools, libraries, package management, and other software.

| Distribution | Package manager | Package format |
|---|---|---|
| Ubuntu | APT | DEB |
| Debian | APT | DEB |
| Rocky Linux | DNF | RPM |
| AlmaLinux | DNF | RPM |
| Red Hat Enterprise Linux | DNF | RPM |
| Fedora | DNF | RPM |
| Alpine Linux | APK | APK |

## 5.1 Identify your distribution

```bash
cat /etc/os-release
```

Check the kernel:

```bash
uname -r
```

Check the CPU architecture:

```bash
uname -m
```

These commands help you select the correct package manager and software instructions.

---

# 6. Linux Boot Process

The boot process starts the machine and brings it to a usable operating state.

```text
Power On
   |
   v
BIOS / UEFI
   |
   v
Bootloader (commonly GRUB)
   |
   v
Linux Kernel
   |
   v
Initial RAM Filesystem (initramfs)
   |
   v
PID 1 / systemd on typical systemd systems
   |
   v
System Services
   |
   v
Login Prompt or Graphical Environment
```

## 6.1 Explanation

1. **BIOS/UEFI:** Initializes hardware and selects a boot device.
2. **Bootloader:** Loads the kernel and the initial RAM filesystem.
3. **Kernel:** Initializes system resources and hardware drivers.
4. **initramfs:** Provides the temporary early userspace needed to find and mount the real root filesystem.
5. **PID 1:** Initializes userspace. On many distributions, this is systemd.
6. **Services:** Start according to system configuration and dependencies.
7. **Login:** The system becomes available for users.

Inspect the current boot:

```bash
uptime
who -b
systemd-analyze time
```

`systemd-analyze` is available on systemd-based systems.

---

# 7. Shell and Terminal

## 7.1 Terminal vs shell

- **Terminal:** The interface where you type commands and see output.
- **Shell:** The program that interprets commands and runs programs.

A terminal emulator may run Bash, but the terminal itself is not Bash.

## 7.2 Check the current user and shell

```bash
whoami
id
echo "$SHELL"
pwd
```

## 7.3 Command structure

```bash
command [options] [arguments]
```

Example:

```bash
ls -lah /var/log
```

- `ls`: Command.
- `-lah`: Options.
- `/var/log`: Argument.

## 7.4 Absolute and relative paths

Absolute path:

```bash
cd /var/log
```

Relative path:

```bash
cd ../tmp
```

An absolute path starts from `/`. A relative path is interpreted from the current working directory.

## 7.5 Command help

```bash
man ls
ls --help
type cd
command -v bash
```

Some commands are shell built-ins and may not have a separate executable file.

---

# 8. Essential Linux Commands

## 8.1 Navigation

```bash
pwd
ls
ls -l
ls -la
cd /etc
cd ..
cd ~
```

## 8.2 File and directory management

```bash
touch file.txt
mkdir project
mkdir -p project/src/scripts
cp file.txt file-backup.txt
mv file-backup.txt backup.txt
rm backup.txt
rmdir empty-directory
```

Be careful with recursive deletion. Never run a destructive command against an unverified path.

## 8.3 Reading files

```bash
cat /etc/os-release
less /var/log/syslog
head -n 10 file.txt
tail -n 20 file.txt
tail -f application.log
wc -l file.txt
```

The location and name of system logs depend on the distribution and logging configuration.

## 8.4 Search for files

```bash
find . -name "*.log"
find /var/log -type f
find . -type f -name "*.sh"
```

Search file contents:

```bash
grep "ERROR" application.log
grep -i "error" application.log
grep -n "failed" application.log
grep -R "TODO" .
```

## 8.5 Pipes and redirection

A pipe sends one command's standard output to another command's standard input.

```bash
ps aux | grep nginx
cat application.log | grep ERROR
```

For a single search, prefer:

```bash
grep "ERROR" application.log
```

Redirection examples:

```bash
echo "Linux practice" > notes.txt
echo "Next topic: networking" >> notes.txt
command > output.txt
command 2> errors.txt
command > output.txt 2>&1
```

- `>` overwrites standard output.
- `>>` appends standard output.
- `2>` redirects standard error.
- `2>&1` redirects standard error to the current standard-output destination.

## 8.6 Exit codes

Most programs use exit code `0` to indicate success and a nonzero code to indicate failure.

```bash
ls /tmp
echo $?
```

Capture an exit code immediately after the command you want to inspect.

## 8.7 Command chaining

```bash
mkdir demo && cd demo
command1 || echo "Command failed"
command1 ; command2
```

- `&&`: Run the next command only if the previous command succeeds.
- `||`: Run the next command if the previous command fails.
- `;`: Run the next command regardless of the previous command's exit status.

---

# 9. Linux Filesystem Hierarchy

| Directory | Purpose |
|---|---|
| `/` | Root of the filesystem |
| `/home` | Regular users' home directories |
| `/root` | Root user's home directory |
| `/etc` | System-wide configuration |
| `/var` | Variable data such as logs, caches, and queues |
| `/var/log` | Common location for log files |
| `/tmp` | Temporary files |
| `/usr` | Many applications, libraries, and shared resources |
| `/bin` | Essential commands on traditional layouts; often linked to `/usr/bin` |
| `/sbin` | System administration commands on traditional layouts; often linked to `/usr/sbin` |
| `/dev` | Device files |
| `/proc` | Virtual filesystem with process and kernel information |
| `/sys` | Kernel and device information |
| `/boot` | Bootloader and kernel-related files |
| `/mnt` | Common temporary mount point |
| `/media` | Common mount location for removable media |
| `/opt` | Optional application software |

Explore the filesystem:

```bash
ls /
ls /etc
ls /var/log
ls /home
```

Remember that `/` is the filesystem root, whereas `/root` is the root user's home directory.

---

# 10. File and Directory Management

## 10.1 File types

Linux supports multiple file types, including:

- Regular files.
- Directories.
- Symbolic links.
- Character devices.
- Block devices.
- Named pipes.
- Unix domain sockets.

Inspect a file:

```bash
ls -l
file filename
stat filename
```

## 10.2 Links

Hard link:

```bash
ln original.txt hardlink.txt
```

Symbolic link:

```bash
ln -s /path/to/original.txt shortcut.txt
```

A symbolic link stores a path reference. A hard link refers to the same underlying filesystem object and generally cannot cross filesystem boundaries.

## 10.3 File checksums

```bash
sha256sum file.txt
```

Checksums can help verify that a file has not changed unexpectedly.

---

# 11. Users, Groups, and Permissions

Linux uses users and groups to control access to resources.

## 11.1 User types

- **Root user:** Has broad administrative privileges.
- **Regular user:** Uses the system with assigned permissions.
- **Service account:** Runs an application or service with a defined identity.

Inspect identities:

```bash
whoami
id
who
groups
```

## 11.2 Account files

```bash
cat /etc/passwd
cat /etc/group
```

Password hashes and related account data are protected in `/etc/shadow` on conventional Linux systems.

## 11.3 Create a user and group

```bash
sudo useradd -m devuser
sudo passwd devuser
sudo groupadd developers
sudo usermod -aG developers devuser
id devuser
```

Distribution defaults differ. On Ubuntu, `adduser` is also commonly available as an interactive account-management utility.

## 11.4 Permissions

Example:

```text
-rwxr-xr--
```

The first character identifies the file type. The next three characters describe owner permissions, the following three describe group permissions, and the last three describe permissions for others.

| Permission | Numeric value | Meaning for regular files |
|---|---:|---|
| Read (`r`) | 4 | Read file contents |
| Write (`w`) | 2 | Modify file contents |
| Execute (`x`) | 1 | Execute the file if it is executable |

For directories, read allows listing entries, write allows changing entries subject to other restrictions, and execute allows traversal/search.

## 11.5 chmod examples

```bash
chmod 644 file.txt
chmod 600 private.txt
chmod 755 script.sh
chmod u+x script.sh
```

| Mode | Owner | Group | Others |
|---|---|---|---|
| `644` | Read/write | Read | Read |
| `600` | Read/write | None | None |
| `755` | Read/write/execute | Read/execute | Read/execute |

## 11.6 Ownership

```bash
ls -l file.txt
sudo chown devuser:developers file.txt
sudo chown -R devuser:developers project/
```

Recursive ownership changes affect every item under the specified path. Verify the target before using `-R`.

## 11.7 sudo

`sudo` runs an authorized command with elevated privileges.

```bash
sudo systemctl status ssh
sudo apt update
```

Use only the privileges needed for the task.

---

# 12. Processes and Process Management

A process is a running instance of a program.

## 12.1 Inspect processes

```bash
ps
ps aux
top
pgrep nginx
pstree
```

Not all distributions install `pstree` by default.

## 12.2 Foreground and background

```bash
sleep 300
```

Press `Ctrl+C` to interrupt a foreground process.

```bash
sleep 300 &
jobs
```

The ampersand starts the command in the background of the current shell.

## 12.3 Signals

```bash
kill PID
kill -TERM PID
kill -KILL PID
```

Replace `PID` with the actual process ID.

`SIGTERM` requests graceful termination. `SIGKILL` forcefully terminates a process and should be used only when necessary because cleanup cannot be performed by the target process.

## 12.4 Resource inspection

```bash
uptime
free -h
top
df -h
```

Linux also uses memory for caches and buffers, so low-looking free memory alone does not necessarily indicate a memory shortage.

---

# 13. Package Management

Package managers install, update, query, and remove software.

## 13.1 Ubuntu and Debian

```bash
sudo apt update
sudo apt upgrade
sudo apt install nginx
apt search nginx
dpkg -l
sudo apt remove nginx
```

`apt update` refreshes package indexes. `apt upgrade` upgrades installed packages where applicable.

## 13.2 RHEL, Rocky Linux, AlmaLinux, and Fedora

```bash
sudo dnf check-update
sudo dnf upgrade
sudo dnf install nginx
dnf search nginx
rpm -qa
sudo dnf remove nginx
```

A package manager may return a special exit code when updates are available, so inspect the output rather than interpreting every nonzero result as a command failure.

## 13.3 Verify installation

```bash
command -v nginx
nginx -v
```

Some applications require elevated privileges or a specific invocation to display their version.

---

# 14. Linux Networking

Networking is critical for connecting servers, applications, containers, and cloud infrastructure.

## 14.1 IP address

An IP address identifies a network interface within an IP network.

IPv4 example:

```text
192.168.1.10
```

IPv4 addresses are 32 bits long.

IPv6 addresses are 128 bits long.

## 14.2 Subnet and CIDR

Example:

```text
192.168.1.10/24
```

The `/24` prefix indicates 24 network bits, leaving 8 host bits.

For a conventional IPv4 `/24` subnet:

- Total addresses: \(2^8 = 256\).
- Traditionally usable host addresses: 254.
- Network address: `192.168.1.0`.
- Broadcast address: `192.168.1.255`.
- Usable host range: `192.168.1.1` to `192.168.1.254`.

Special-purpose ranges, including `/31` point-to-point links and `/32` host routes, have different considerations.

## 14.3 Inspect network configuration

```bash
ip addr
ip link
ip route
hostname -I
```

## 14.4 Connectivity and DNS

```bash
ping -c 4 8.8.8.8
getent hosts example.com
```

If available:

```bash
dig example.com
nslookup example.com
```

A failed ping does not conclusively prove that a host is down. ICMP may be filtered.

## 14.5 Listening ports

```bash
ss -tuln
ss -tulpn
```

Options commonly used:

- `-t`: TCP sockets.
- `-u`: UDP sockets.
- `-l`: Listening sockets.
- `-n`: Numeric addresses and ports.
- `-p`: Show process information when available and permitted.

## 14.6 Test a TCP port

```bash
curl -I http://localhost:8080
```

If available:

```bash
nc -vz server.example.com 443
```

A successful TCP connection indicates that a connection can be established to that endpoint; it does not guarantee that the application is healthy.

## 14.7 Networking troubleshooting order

1. Is the network interface up?
2. Does the host have the expected IP address?
3. Is the subnet correct?
4. Is the default route present?
5. Can the host reach the next network hop?
6. Does DNS resolution work?
7. Is the destination service listening?
8. Are firewalls, security groups, routing rules, or network policies blocking traffic?

---

# 15. Services and systemd

A service is a background component that provides functionality, such as SSH access or web hosting.

Many modern Linux distributions use systemd as their service manager.

## 15.1 Check a service

```bash
systemctl status ssh
```

On some distributions the service is named `sshd` instead of `ssh`.

## 15.2 Manage services

```bash
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
sudo systemctl enable nginx
sudo systemctl disable nginx
```

`reload` works only if the service supports reloading its configuration.

## 15.3 Start at boot

```bash
sudo systemctl enable nginx
```

Enable and start immediately:

```bash
sudo systemctl enable --now nginx
```

## 15.4 Inspect service failures

```bash
systemctl status nginx
journalctl -u nginx -n 50
```

Check configuration and logs before restarting a production service.

---

# 16. Logs and Troubleshooting

Logs record system and application events.

## 16.1 Common log locations

| Location | Typical contents |
|---|---|
| `/var/log/` | System and application logs |
| `/var/log/syslog` | General system logs on some Debian-based distributions |
| `/var/log/messages` | General system logs on some RHEL-based distributions |
| `/var/log/auth.log` | Authentication events on some Debian-based systems |
| `/var/log/secure` | Authentication events on some RHEL-based systems |
| `journalctl` | Logs stored in the systemd journal |

Log paths depend on the distribution and logging configuration.

## 16.2 Inspect logs

```bash
journalctl -n 100
journalctl -p err
journalctl -u nginx
journalctl -u nginx --since "1 hour ago"
```

Follow live logs:

```bash
journalctl -u nginx -f
```

## 16.3 Search logs

```bash
grep -i "error" application.log
grep -i "failed" application.log
tail -n 100 application.log
```

## 16.4 Troubleshooting methodology

Use a repeatable approach:

1. Identify the symptom.
2. Determine the affected service or host.
3. Check service status.
4. Inspect logs around the failure time.
5. Check processes and resource usage.
6. Verify network connectivity.
7. Validate configuration.
8. Make the smallest safe change.
9. Test the service again.
10. Document the root cause and prevention.

---

# 17. Linux Storage Management

## 17.1 Disk and filesystem commands

```bash
lsblk
df -h
df -i
du -sh /var/log
findmnt
blkid
```

| Command | Purpose |
|---|---|
| `lsblk` | Lists block devices |
| `df -h` | Displays filesystem space usage |
| `df -i` | Displays inode usage |
| `du -sh` | Summarizes disk usage for a path |
| `findmnt` | Displays mount information |
| `blkid` | Shows filesystem identification data |

## 17.2 Disk space vs inode exhaustion

A server can fail to create files when:

- The filesystem has no free space.
- The filesystem has exhausted its inodes.

Check both:

```bash
df -h
df -i
```

## 17.3 Find large directories

```bash
sudo du -xhd1 /var 2>/dev/null
```

On systems whose `du` supports these options, this summarizes immediate subdirectories without crossing filesystem boundaries.

## 17.4 Mounting a filesystem

Example only:

```bash
sudo mkdir -p /mnt/data
sudo mount /dev/sdX1 /mnt/data
findmnt /mnt/data
sudo umount /mnt/data
```

`/dev/sdX1` is a placeholder. Identify the correct device and filesystem first. Do not format, repartition, or mount an unknown production disk based on a copied example.

---

# 18. Environment Variables

Environment variables configure the behavior of shells and applications.

## 18.1 Inspect variables

```bash
echo "$HOME"
echo "$USER"
echo "$PATH"
env
printenv
```

## 18.2 Set a variable

```bash
APP_ENV=development
echo "$APP_ENV"
```

This variable exists in the current shell.

Export it so child processes can inherit it:

```bash
export APP_ENV=development
```

## 18.3 PATH

`PATH` contains directories searched when the shell looks for an executable command.

```bash
echo "$PATH"
command -v bash
```

## 18.4 Variables in DevOps

Environment variables are frequently used to configure:

- Application environments.
- Build tools.
- CI/CD jobs.
- Containerized applications.
- Cloud CLI behavior.
- Automation scripts.

Do not print secrets or credentials into public logs. Use approved secret-management mechanisms.

---

# 19. Bash Scripting Basics

Bash scripts automate repetitive operations.

## 19.1 Create a script

Create `hello.sh`:

```bash
#!/usr/bin/env bash
set -euo pipefail

echo "Hello from Linux"
echo "Current user: $(whoami)"
echo "Current directory: $(pwd)"
```

Make it executable:

```bash
chmod +x hello.sh
./hello.sh
```

## 19.2 Variables

```bash
#!/usr/bin/env bash

APP_NAME="nginx"
echo "Application: $APP_NAME"
```

## 19.3 Conditional statements

```bash
#!/usr/bin/env bash

if [ -f "/etc/os-release" ]; then
    echo "Linux OS information is available"
else
    echo "OS information file not found"
fi
```

## 19.4 Loops

```bash
#!/usr/bin/env bash

for item in file1.txt file2.txt file3.txt; do
    echo "Processing $item"
done
```

## 19.5 Exit status

```bash
#!/usr/bin/env bash

if grep -q "ERROR" application.log; then
    echo "Errors found"
    exit 1
else
    echo "No matching errors found"
fi
```

## 19.6 Why Bash matters in DevOps

Bash is useful for:

- Running repeatable administration tasks.
- Preparing build environments.
- Automating deployments.
- Checking service health.
- Processing logs.
- Validating configuration.
- Running commands in CI/CD pipelines.

For complex applications, structured Python programs or dedicated automation tools may be more maintainable than large shell scripts.

---

# 20. Linux Security

Security is a core responsibility in infrastructure management.

## 20.1 Least privilege

Grant only the access needed to complete a task.

Examples:

- Avoid using root for routine work.
- Use dedicated service accounts.
- Restrict access to sensitive files.
- Grant administrative access selectively.
- Avoid unnecessary privileges in containers.

## 20.2 File permissions

```bash
chmod 600 private-key
chmod 644 public-file.txt
```

For SSH private keys, follow the requirements of the SSH client and your organization's security policy.

## 20.3 SSH

SSH enables secure remote administration.

Example:

```bash
ssh user@server.example.com
```

For key-based authentication:

```bash
ssh -i ~/.ssh/id_ed25519 user@server.example.com
```

Never share private keys or commit them to a public Git repository.

## 20.4 Firewall basics

Linux systems may use tools such as:

- `ufw` on some Ubuntu installations.
- `firewalld` on many RHEL-family systems.
- `nftables` or other packet-filtering mechanisms.

Inspect the firewall tool actually configured on the machine before changing rules. Incorrect firewall changes can interrupt SSH or application access.

## 20.5 Security practices

- Keep packages updated according to an approved maintenance policy.
- Use SSH keys and appropriate access controls.
- Review users, groups, and privileged access.
- Protect credentials and private keys.
- Inspect authentication logs.
- Remove unnecessary services.
- Validate permissions on application files.
- Back up critical data and test recovery procedures.

---

# 21. Linux in DevOps

This section connects Linux fundamentals to real infrastructure workflows.

## 21.1 Provisioning a Linux server

A typical server setup includes:

1. Create a VM or cloud instance.
2. Configure network access and SSH.
3. Verify the operating system.
4. Update packages.
5. Create appropriate users.
6. Configure permissions.
7. Install application dependencies.
8. Configure and start services.
9. Verify listening ports.
10. Configure logging and monitoring.

Useful initial checks:

```bash
cat /etc/os-release
uname -r
hostname
ip addr
ip route
df -h
free -h
uptime
```

## 21.2 Application deployment

A simple Linux-hosted application workflow:

```text
Source Code
    |
    v
CI/CD Pipeline
    |
    v
Build and Test
    |
    v
Package or Container Image
    |
    v
Deploy to Target Environment
    |
    v
Start or Update Application
    |
    v
Health Checks
    |
    v
Logs and Monitoring
```

Linux commands help engineers inspect each stage and investigate deployment failures.

## 21.3 Linux and Git

Git commands are commonly executed from a shell:

```bash
git status
git add .
git commit -m "Add Linux study notes"
git log --oneline
```

Use `.gitignore` to prevent committing local artifacts and secrets. Inspect changes before committing.

## 21.4 Linux and CI/CD

A pipeline might run commands such as:

```bash
git clone REPOSITORY_URL
cd project
./build.sh
./test.sh
```

`REPOSITORY_URL` is a placeholder. Replace it with the intended repository URL.

CI/CD jobs commonly use environment variables, shell scripts, filesystem permissions, process exit codes, and network access. Linux troubleshooting is therefore useful when a pipeline fails.

## 21.5 Linux and configuration management

Tools such as Ansible use SSH and Linux administration capabilities to configure multiple hosts.

Typical managed operations include:

- Installing packages.
- Creating users.
- Managing files and permissions.
- Configuring services.
- Deploying application artifacts.
- Validating system state.

## 21.6 Linux and infrastructure as code

Terraform can provision cloud infrastructure, including Linux VMs, networks, and storage. Linux knowledge becomes important when connecting to those VMs, configuring software, and troubleshooting the deployed environment.

---

# 22. Linux in Docker and Kubernetes

## 22.1 Linux and Docker

Linux containers use kernel capabilities to isolate workloads. Important technologies include:

- **Namespaces:** Provide isolation for resources such as processes and networking.
- **cgroups:** Account for and control resource usage.
- **Capabilities:** Divide traditionally broad root privileges into more granular permissions.
- **Filesystem layers:** Help build and distribute container images.
- **Networking:** Connect containers to other containers and external systems.

A container is not necessarily a complete virtual machine. Containers generally share the host kernel.

Basic commands:

```bash
docker ps
docker images
docker logs CONTAINER_NAME
docker inspect CONTAINER_NAME
```

Replace `CONTAINER_NAME` with the actual container name or ID.

## 22.2 Why Linux knowledge helps with Docker

Linux concepts help troubleshoot:

- Container processes that exit unexpectedly.
- Incorrect file permissions.
- Port-binding problems.
- DNS failures.
- Volume mount issues.
- Memory and CPU limits.
- Missing environment variables.
- Application logs.

## 22.3 Linux and Kubernetes

Kubernetes commonly runs Linux-based worker nodes and workloads.

Linux knowledge helps investigate:

- Pod networking.
- Container startup failures.
- Node resource pressure.
- Volume mounts.
- DNS resolution.
- Container runtime problems.
- Service connectivity.

Example commands:

```bash
kubectl get pods -A
kubectl describe pod POD_NAME
kubectl logs POD_NAME
kubectl get nodes
```

Replace placeholders with actual resource names.

Kubernetes commands are not Linux commands, but they are often used alongside Linux tools when diagnosing production issues.

---

# 23. Linux in Cloud Engineering and SRE

## 23.1 Linux in cloud engineering

Linux administration is relevant when managing cloud VMs and services.

Common responsibilities include:

- SSH access to virtual machines.
- Package installation and updates.
- User and key management.
- Application configuration.
- Storage and filesystem maintenance.
- Network troubleshooting.
- Service management.
- Log inspection.

Cloud firewalls, security groups, virtual networks, and routing tables must also be checked when Linux networking alone does not explain a connectivity failure.

## 23.2 Linux in Site Reliability Engineering

SRE focuses on reliability, availability, performance, monitoring, and operational learning.

Linux skills support:

- Incident investigation.
- Capacity planning.
- Resource monitoring.
- Performance analysis.
- Service recovery.
- Log analysis.
- Automation.
- Post-incident troubleshooting.

Useful commands:

```bash
uptime
top
free -h
df -h
df -i
iostat
vmstat
ss -s
```

Some commands, including `iostat`, may require installing additional packages.

## 23.3 Resource troubleshooting

| Symptom | Initial investigation |
|---|---|
| High CPU | `top`, `ps aux` |
| Memory pressure | `free -h`, `top`, `vmstat` |
| Disk full | `df -h`, `du -sh` |
| Inodes exhausted | `df -i` |
| Service unavailable | `systemctl status`, `journalctl` |
| Port unavailable | `ss -tulpn` |
| DNS failure | `getent hosts`, `dig` if available |
| Slow network | Check routes, packet loss, latency, and interface counters |
| Failed SSH | Check SSH service, firewall, routing, keys, and authentication logs |

Use these commands to gather evidence before changing system configuration.

---

# 24. Real-World Troubleshooting Scenarios

## Scenario 1: A service is not starting

**Symptoms:** An application is unavailable after a deployment.

Investigation:

```bash
systemctl status nginx
journalctl -u nginx -n 100
ss -tulpn
```

Check:

1. Is the service installed?
2. Is its configuration valid?
3. Does the service report an error?
4. Is the required port already occupied?
5. Are required files and directories accessible?
6. Are required dependencies running?

**Learning:** A service restart is not a substitute for identifying the root cause.

## Scenario 2: A Linux server has no internet access

Investigation:

```bash
ip addr
ip route
ping -c 4 8.8.8.8
getent hosts example.com
```

Interpretation:

- No IP address: Investigate interface configuration and DHCP or static addressing.
- No default route: Investigate gateway and route configuration.
- IP connectivity works but DNS fails: Investigate DNS configuration and resolution.
- Both fail: Investigate routing, firewall rules, and upstream connectivity.

Ping results alone are not conclusive.

## Scenario 3: Disk space is full

Investigation:

```bash
df -h
df -i
sudo du -xhd1 /var 2>/dev/null
```

Check for:

- Large logs.
- Application-generated files.
- Unexpected temporary data.
- Container image and volume usage.
- Deleted files that remain open by running processes.

Do not delete arbitrary files from `/var/lib`, container storage, or system directories without understanding their purpose.

## Scenario 4: Permission denied

Investigation:

```bash
whoami
id
ls -l file.txt
namei -l /path/to/file.txt
```

Check:

- File ownership.
- File permissions.
- Directory traversal permissions.
- Group membership.
- ACLs, if configured.
- Whether the application is running under the expected user.

Avoid solving every permission issue with `chmod 777`.

## Scenario 5: A deployment script fails

Investigation:

```bash
bash -n deploy.sh
bash -x deploy.sh
echo $?
```

- `bash -n`: Checks shell syntax without executing the script.
- `bash -x`: Prints commands as the script executes them.
- `echo $?`: Shows the last command's exit status.

Use trace output carefully because it can expose secrets or sensitive command arguments.

## Scenario 6: An application is unreachable on port 8080

Investigation:

```bash
ss -tulpn
curl -v http://localhost:8080
```

Then check:

1. Is the application listening?
2. Is it listening on the expected interface?
3. Is the service healthy?
4. Are host firewall rules allowing traffic?
5. Are cloud security groups and network ACLs configured correctly?
6. Is the correct port published or forwarded?
7. Is a reverse proxy configured correctly?

---

# 25. Hands-on Practice Labs

Use a disposable Linux VM or a practice environment where you have permission to make changes.

## Lab 1: Linux identification

Objectives:

- Identify the distribution.
- Identify the kernel.
- Find the current user and hostname.

Commands:

```bash
cat /etc/os-release
uname -r
whoami
hostname
pwd
```

Record the output and explain each result.

## Lab 2: Files and directories

```bash
mkdir -p linux-lab/logs
touch linux-lab/logs/app.log
echo "Application started" > linux-lab/logs/app.log
cat linux-lab/logs/app.log
find linux-lab -type f
```

Objectives:

- Create directories.
- Create and read a file.
- Search for files.

## Lab 3: Permissions

```bash
mkdir permission-lab
cd permission-lab
touch private.txt
chmod 600 private.txt
ls -l private.txt
```

Explain why the file is not readable by group members or other users under normal Unix permission checks.

## Lab 4: Process inspection

```bash
sleep 300 &
jobs
ps aux | grep '[s]leep'
```

Identify the process ID, then stop the test process:

```bash
kill PID
```

Replace `PID` with the actual process ID.

## Lab 5: Networking

```bash
ip addr
ip route
getent hosts example.com
ss -tuln
```

Record:

- The primary network interface.
- The assigned IP address.
- The default route, if present.
- Whether hostname resolution works.
- Which TCP/UDP ports are listening.

## Lab 6: Package management

Identify the distribution:

```bash
cat /etc/os-release
```

On Debian or Ubuntu:

```bash
sudo apt update
apt search curl
```

On a DNF-based distribution:

```bash
sudo dnf check-update
dnf search curl
```

Install packages only when needed and permitted.

## Lab 7: Service troubleshooting

Use an installed service appropriate for your distribution:

```bash
systemctl status ssh
journalctl -u ssh -n 50
```

The service might instead be named `sshd`, or it might not be installed.

Record the service state and explain the important log messages.

## Lab 8: Disk inspection

```bash
lsblk
df -h
df -i
du -sh "$HOME"
```

Explain the difference between block storage capacity and inode availability.

## Lab 9: Basic Bash script

Create a script that prints:

- Current username.
- Hostname.
- Current directory.
- Current date.
- Kernel version.

Example:

```bash
#!/usr/bin/env bash
set -euo pipefail

echo "User: $(whoami)"
echo "Hostname: $(hostname)"
echo "Directory: $(pwd)"
echo "Date: $(date)"
echo "Kernel: $(uname -r)"
```

Save it as `system-info.sh`, make it executable, and run it.

## Lab 10: DevOps troubleshooting exercise

Simulate a simple application outage in a disposable environment.

Document:

1. The initial symptom.
2. The commands used.
3. The evidence collected.
4. The root cause.
5. The corrective action.
6. The verification performed.
7. The prevention or monitoring improvement.

---

# 26. Common Linux Commands Cheat Sheet

| Task | Command |
|---|---|
| Current directory | `pwd` |
| Current user | `whoami` |
| System information | `uname -a` |
| Distribution information | `cat /etc/os-release` |
| List files | `ls -lah` |
| Find files | `find . -name "*.log"` |
| Search text | `grep -n "ERROR" app.log` |
| Copy files | `cp source destination` |
| Move files | `mv source destination` |
| File permissions | `chmod 644 file.txt` |
| File ownership | `chown user:group file.txt` |
| Process listing | `ps aux` |
| CPU and process monitoring | `top` |
| Memory usage | `free -h` |
| Disk capacity | `df -h` |
| Inode usage | `df -i` |
| Disk usage by path | `du -sh /path` |
| IP configuration | `ip addr` |
| Routing table | `ip route` |
| Listening sockets | `ss -tulpn` |
| DNS resolution | `getent hosts example.com` |
| Service status | `systemctl status SERVICE` |
| Service logs | `journalctl -u SERVICE` |
| Recent system logs | `journalctl -n 100` |
| Package identification | `cat /etc/os-release` |
| Shell syntax validation | `bash -n script.sh` |
| Script tracing | `bash -x script.sh` |
| Current uptime | `uptime` |
| Exit status | `echo $?` |

---

# 27. Interview Questions

## Linux fundamentals

1. What is Linux?
2. What is the difference between Linux and a Linux distribution?
3. What is the role of the Linux kernel?
4. What is the difference between a terminal and a shell?
5. What is the difference between an absolute and relative path?
6. Explain the Linux filesystem hierarchy.
7. What is the difference between a process and a program?

## Commands and permissions

8. What is the difference between `>` and `>>`?
9. What is the purpose of pipes?
10. What do `chmod 644` and `chmod 755` mean?
11. What is the difference between `chmod` and `chown`?
12. What is the purpose of `sudo`?
13. How do you find a file or search its contents?

## Networking and services

14. How do you check an IP address in Linux?
15. How do you inspect listening ports?
16. How would you troubleshoot a DNS failure?
17. What is the difference between `systemctl start` and `systemctl enable`?
18. How do you inspect service logs?
19. How would you investigate a service that fails to start?

## Storage and performance

20. What is the difference between `df` and `du`?
21. What is an inode?
22. How do you investigate high CPU usage?
23. How do you investigate memory pressure?
24. What would you do if a filesystem were full?

## Linux in DevOps

25. Why is Linux important in DevOps?
26. How is Linux used in CI/CD pipelines?
27. Why do containers depend on Linux kernel features?
28. How would you troubleshoot a Docker container that exits immediately?
29. How does Linux knowledge help in Kubernetes troubleshooting?
30. What Linux commands would you use during a production incident?

**Interview practice method:** Answer each question in your own words, provide a real command example, and explain when you would use it in a production environment.

---

# 28. Learning Log

Use this template for every study session.

## Session: Linux Fundamentals

**Date:** YYYY-MM-DD

**Course:** KodeKloud — Learning Linux Basics Course & Labs

**Module:**

**Topics studied:**
- 

**Commands practiced:**
```bash
# Add commands actually executed during the session
```

**What I understood:**
- 

**What I found difficult:**
- 

**Errors encountered:**
- 

**Root cause:**
- 

**Solution:**
- 

**DevOps relevance:**
- 

**Interview questions to revise:**
- 

**Next session goals:**
- 

---

# Final Learning Roadmap

- [ ] Understand Linux fundamentals and architecture.
- [ ] Become comfortable with the shell and essential commands.
- [ ] Practice files, directories, permissions, users, and groups.
- [ ] Understand processes, services, and systemd.
- [ ] Learn package management.
- [ ] Practice networking, DNS, ports, and troubleshooting.
- [ ] Learn storage and filesystem administration.
- [ ] Understand logs, security, and system monitoring.
- [ ] Write and execute Bash scripts.
- [ ] Troubleshoot common Linux server failures.
- [ ] Connect Linux knowledge to Git, CI/CD, and automation.
- [ ] Practice Docker and Kubernetes troubleshooting.
- [ ] Review interview questions and document practical labs.

## Key Takeaway

Linux is not just a collection of commands. It is the foundation for understanding how applications run, how servers communicate, how permissions are enforced, how resources are managed, and how infrastructure failures are investigated.

For DevOps and SRE, focus on understanding the **reason behind each command**, observing its output, practicing in a safe environment, and learning how to troubleshoot problems systematically.
