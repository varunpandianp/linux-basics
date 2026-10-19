# Linux Package Management — RPM, YUM, DNF, DPKG and APT

## 1. What Is Package Management?

Package management is the process of installing, updating, upgrading, removing, searching for, and verifying software on Linux.

A **package** contains software files, metadata, and sometimes scripts needed to install and manage an application.

Examples:
- `wget` — downloads files from the internet.
- `tcpdump` — captures and analyzes network packets.
- `firefox` — web browser.
- `nginx` — web server.
- `docker` — container platform components.

## 2. Package Managers in Linux

Linux distributions use different package formats and management tools.

| Distribution family | Package format | Low-level tool | Higher-level manager |
|---|---|---|---|
| CentOS / RHEL | `.rpm` | `rpm` | `yum` / `dnf` |
| Fedora | `.rpm` | `rpm` | `dnf` |
| Ubuntu | `.deb` | `dpkg` | `apt` |
| Debian | `.deb` | `dpkg` | `apt` |

### Important difference

- **RPM and DPKG:** Query, install, and remove packages, but do not automatically resolve all dependencies from repositories.
- **YUM, DNF, and APT:** Work with repositories and can resolve dependencies automatically.

Remember:

```text
CentOS / RHEL → RPM + YUM/DNF
Ubuntu / Debian → DPKG + APT
```

---

## 3. RPM — Red Hat Package Manager

RPM manages `.rpm` packages on CentOS, RHEL, Fedora, and related distributions.

### 3.1 Check whether a package is installed

```bash
rpm -q wget
rpm -q firefox
```

Example output:

```text
wget-1.14-18.el7.x86_64
```

The exact version and release depend on the system.

### 3.2 Install a local RPM package

```bash
sudo rpm -ivh package.rpm
```

Options:
- `-i` — install.
- `-v` — verbose output.
- `-h` — display progress using hash marks.

### 3.3 Upgrade a local RPM package

```bash
sudo rpm -Uvh package.rpm
```

`-U` installs or upgrades the package.

### 3.4 Remove a package

```bash
sudo rpm -e wget
```

Use the package name, not the downloaded RPM filename.

### 3.5 List installed packages

```bash
rpm -qa
```

Search the installed package list:

```bash
rpm -qa | grep -i wget
```

### 3.6 Find the package that owns a command or file

```bash
rpm -qf /usr/bin/wget
```

To locate the command first:

```bash
which wget
rpm -qf "$(which wget)"
```

This requires the file to exist and be owned by an installed RPM package.

### 3.7 Query information about an installed package

```bash
rpm -qi wget
```

### 3.8 List files installed by a package

```bash
rpm -ql wget
```

### 3.9 Inspect a downloaded RPM file

```bash
rpm -qp package.rpm
rpm -qpi package.rpm
rpm -qpl package.rpm
```

- `-qp` — query a package file.
- `-qpi` — display package information.
- `-qpl` — list files contained in the package.

### 3.10 Verify an installed package

```bash
rpm -V wget
```

Checks installed package files against RPM database metadata. No output generally means no differences were found in the attributes checked.

---

## 4. YUM — Yellowdog Updater, Modified

YUM is a higher-level package manager commonly used on older CentOS and RHEL systems. It uses RPM packages and resolves dependencies through configured repositories.

### 4.1 Install a package

```bash
sudo yum install wget
sudo yum install nginx
```

### 4.2 Remove a package

```bash
sudo yum remove wget
```

### 4.3 Update package metadata

```bash
sudo yum makecache
```

This refreshes the local repository metadata cache.

### 4.4 Update installed packages

```bash
sudo yum update
```

Updates installed packages for which newer versions are available.

### 4.5 Update one specific package

```bash
sudo yum update wget
```

### 4.6 Search for a package

```bash
yum search wget
yum search firefox
```

### 4.7 Display package information

```bash
yum info wget
```

### 4.8 List installed packages

```bash
yum list installed
```

Search for a particular installed package:

```bash
yum list installed wget
```

### 4.9 List available packages

```bash
yum list available
```

### 4.10 List configured repositories

```bash
yum repolist
yum repolist enabled
yum repolist all
```

- `yum repolist` — lists enabled repositories.
- `yum repolist all` — includes enabled and disabled repositories.

### 4.11 Find which package provides a command

```bash
yum provides '*/tcpdump'
```

This searches repository package metadata to identify packages that provide the requested file or command.

### 4.12 Install a downloaded RPM package with dependency resolution

```bash
sudo yum localinstall /path/to/package.rpm
```

On many YUM versions, `yum install /path/to/package.rpm` also works. YUM can resolve dependencies from configured repositories when available.

---

## 5. DNF — Dandified YUM

DNF is the modern successor to YUM and is used by current Fedora releases and newer RHEL-family systems.

Many commands are similar to YUM.

### Install a package

```bash
sudo dnf install wget
```

### Remove a package

```bash
sudo dnf remove wget
```

### Upgrade packages

```bash
sudo dnf upgrade
```

### Search for packages

```bash
dnf search nginx
```

### Display package information

```bash
dnf info wget
```

### List installed packages

```bash
dnf list installed
```

### List repositories

```bash
dnf repolist
dnf repolist --all
```

### Find which package provides a command

```bash
dnf provides '*/tcpdump'
```

**Remember:** Use the package manager available on your distribution. Some systems provide YUM as a compatibility interface backed by DNF.

---

## 6. DPKG — Debian Package Manager

DPKG is the low-level package management tool used by Debian and Ubuntu. It manages `.deb` package files.

### 6.1 Install a downloaded DEB package

```bash
sudo dpkg -i package.deb
```

Example:

```bash
sudo dpkg -i /root/firefox.deb
```

The file must exist at the specified path.

### 6.2 Remove a package

```bash
sudo dpkg -r firefox
```

Removes the package while generally leaving its system configuration files behind.

### 6.3 Purge a package

```bash
sudo dpkg -P firefox
```

Removes the package and its package-managed configuration files.

### 6.4 List installed packages

```bash
dpkg -l
```

Search for a package:

```bash
dpkg -l | grep -i firefox
```

### 6.5 Query whether a package is installed

```bash
dpkg -s firefox
```

Displays package status and information if the package is known to the local package database.

### 6.6 Find the package that owns a file

```bash
dpkg -S /usr/bin/wget
```

This identifies the installed package that owns the specified file.

### 6.7 List files installed by a package

```bash
dpkg -L wget
```

### 6.8 Inspect a downloaded DEB package

```bash
dpkg-deb -f package.deb Package
dpkg-deb -f package.deb Version
dpkg-deb -I package.deb
dpkg-deb -c package.deb
```

- `-f ... Package` — displays the exact package name.
- `-f ... Version` — displays the package version.
- `-I` — displays package metadata.
- `-c` — lists files inside the archive.

### 6.9 Verify package status

```bash
dpkg --audit
```

Reports partially installed or inconsistent package states that require attention. It is not a complete file-integrity verification equivalent to `rpm -V`.

---

## 7. APT — Advanced Package Tool

APT is the higher-level package manager used by Debian and Ubuntu. It works with `.deb` packages and repository metadata.

### 7.1 Update repository metadata

```bash
sudo apt update
```

This refreshes information about available packages. It does not itself upgrade installed software.

### 7.2 Install a package

```bash
sudo apt install wget
sudo apt install nginx
```

### 7.3 Remove a package

```bash
sudo apt remove firefox
```

### 7.4 Purge a package

```bash
sudo apt purge firefox
```

Removes the package and its package-managed configuration files.

### 7.5 Upgrade installed packages

```bash
sudo apt upgrade
```

Upgrades installed packages when possible without removing installed packages to satisfy the upgrade.

### 7.6 Upgrade one package

```bash
sudo apt install --only-upgrade wget
```

### 7.7 Search for a package by name or description

```bash
apt search chromium
apt search tcpdump
```

Example description:

```text
Chromium web browser, open-source version of Chrome
```

The package name should be taken from the matching result.

### 7.8 Display package information

```bash
apt show wget
```

### 7.9 List installed packages

```bash
apt list --installed
```

Search for one package:

```bash
apt list --installed 2>/dev/null | grep -i wget
```

### 7.10 Find which package provides a command

If the command is already installed:

```bash
dpkg -S "$(which tcpdump)"
```

If the command is not installed, search configured repository metadata with:

```bash
apt-file search bin/tcpdump
```

`apt-file` may need to be installed and its index updated first. It searches package file lists, unlike `apt search`, which searches package names and descriptions.

### 7.11 Fix broken dependencies

```bash
sudo apt --fix-broken install
```

This asks APT to resolve broken package dependencies using available repositories. It does not automatically install a local DEB file unless you explicitly provide that file in an installation command.

### 7.12 Clean package cache

```bash
sudo apt clean
sudo apt autoclean
sudo apt autoremove
```

- `clean` — removes downloaded package files from the local cache.
- `autoclean` — removes cached packages that can no longer be downloaded.
- `autoremove` — removes automatically installed dependencies no longer needed.

Review the proposed removals before confirming.

### 7.13 List APT repositories

Repository configuration can be found in:

```bash
/etc/apt/sources.list
/etc/apt/sources.list.d/
```

`apt` does not use the same `yum repolist` command. Inspect configured sources and the output of:

```bash
apt update
```

---

## 8. Package Status and Verification

### Check installation status

On CentOS/RHEL/Fedora-family systems:

```bash
rpm -q wget
```

On Debian/Ubuntu:

```bash
dpkg -s wget
```

### Check installed package version

RPM:

```bash
rpm -q wget
```

APT/DPKG:

```bash
dpkg-query -W wget
```

APT repository information:

```bash
apt show wget
```

### Verify that a command exists

```bash
command -v wget
wget --version
```

A package can be installed even if its command is not in your current `PATH`, so use the package database when you need definitive installation status.

---

## 9. Installing a Local Package vs Installing from a Repository

### Install from a repository

CentOS/RHEL:

```bash
sudo yum install wget
```

Debian/Ubuntu:

```bash
sudo apt install wget
```

The package manager searches configured repositories and resolves dependencies.

### Install a downloaded RPM file

```bash
sudo rpm -ivh /path/to/package.rpm
```

Or use the higher-level manager when available:

```bash
sudo yum localinstall /path/to/package.rpm
```

### Install a downloaded DEB file

```bash
sudo dpkg -i /path/to/package.deb
```

Or, on modern APT systems:

```bash
sudo apt install /path/to/package.deb
```

APT can resolve dependencies from configured repositories when possible.

**Important:** A `.deb` file is not installed with RPM/YUM, and an `.rpm` file is not installed with DPKG/APT.

---

## 10. Common Errors and Solutions

### Error: Package is not available

Example:

```text
Package firefox is not available
```

Possible causes:
- The package is not present in enabled repositories.
- Repository metadata is outdated.
- The package has been renamed or removed.
- The distribution does not provide that package in its configured sources.

Useful commands:

```bash
sudo apt update
apt search firefox
```

Or for YUM:

```bash
yum search firefox
yum repolist
```

### Error: Cannot access archive

Example:

```text
dpkg: error: cannot access archive 'firefox': No such file or directory
```

Cause: The command uses the wrong filename or path.

Check the actual file:

```bash
ls -l /root/firefox.deb
```

Then use:

```bash
sudo dpkg -i /root/firefox.deb
```

### Error: Package is not installed

Example:

```text
package firefox is not installed
```

The package database does not show that package as installed. Check the exact package name and the installation status.

### Error: Dependency problems

For Debian/Ubuntu:

```bash
sudo apt --fix-broken install
```

For CentOS/RHEL with YUM/DNF, use the higher-level package manager to install the package so dependencies can be resolved from configured repositories.

---

## 11. Quick Comparison Table

| Task | RPM | YUM/DNF | DPKG | APT |
|---|---|---|---|---|
| Install from repository | — | `install package` | — | `install package` |
| Install local file | `-ivh file.rpm` | `localinstall file.rpm` | `-i file.deb` | `install ./file.deb` |
| Remove | `-e package` | `remove package` | `-r package` | `remove package` |
| Purge config | No direct equivalent | Usually remove; config handling varies | `-P package` | `purge package` |
| Upgrade packages | `-Uvh file.rpm` | `update` / `upgrade` | — | `upgrade` |
| List installed | `-qa` | `list installed` | `-l` | `list --installed` |
| Query package | `-q package` | `info package` | `-s package` | `show package` |
| Search packages | — | `search term` | — | `search term` |
| List repositories | — | `repolist` | — | Inspect APT sources |
| Find package owning file | `-qf /path` | `provides` for repository files | `-S /path` | `dpkg -S /path` for installed files |

---

## 12. DevOps Interview Questions

**1. What is the difference between RPM and YUM?**

RPM is a low-level package tool that queries and manages RPM packages. YUM is a higher-level manager that uses repositories and resolves dependencies.

**2. What is the difference between DPKG and APT?**

DPKG manages local Debian packages. APT manages packages through repositories and handles dependency resolution.

**3. What is the difference between `apt update` and `apt upgrade`?**

`apt update` refreshes repository metadata. `apt upgrade` installs newer versions of installed packages when possible.

**4. What is the difference between `apt remove` and `apt purge`?**

`apt remove` removes the application package but generally leaves system configuration files. `apt purge` also removes package-managed configuration files.

**5. How do you find which package owns a command?**

RPM:

```bash
rpm -qf "$(command -v wget)"
```

DPKG:

```bash
dpkg -S "$(command -v wget)"
```

**6. How do you find the exact name inside a downloaded DEB file?**

```bash
dpkg-deb -f /path/to/package.deb Package
```

**7. How do you list enabled YUM repositories?**

```bash
yum repolist
```

**8. Why use APT or YUM instead of DPKG or RPM for normal installations?**

APT and YUM/DNF can resolve dependencies from configured repositories, making software installation and maintenance easier.

---

## 13. Quick Command Cheat Sheet

```bash
# RPM
rpm -q wget
rpm -qa
rpm -qi wget
rpm -ql wget
rpm -qf /usr/bin/wget
rpm -qp package.rpm
sudo rpm -ivh package.rpm
sudo rpm -Uvh package.rpm
sudo rpm -e wget
rpm -V wget

# YUM
sudo yum install wget
sudo yum remove wget
sudo yum update
yum search wget
yum info wget
yum list installed
yum repolist
yum repolist all
yum provides '*/tcpdump'

# DNF
sudo dnf install wget
sudo dnf remove wget
sudo dnf upgrade
dnf search wget
dnf info wget
dnf repolist

# DPKG
sudo dpkg -i package.deb
sudo dpkg -r firefox
sudo dpkg -P firefox
dpkg -l
dpkg -s wget
dpkg -L wget
dpkg -S /usr/bin/wget
dpkg-deb -f package.deb Package
dpkg --audit

# APT
sudo apt update
sudo apt install wget
sudo apt remove firefox
sudo apt purge firefox
sudo apt upgrade
apt search chromium
apt show wget
apt list --installed
sudo apt --fix-broken install
sudo apt autoremove
```

## Learning Summary

- **RPM/YUM/DNF:** Used primarily in CentOS, RHEL, and Fedora-family distributions.
- **DPKG/APT:** Used in Debian and Ubuntu.
- **Low-level tools:** `rpm` and `dpkg`.
- **Higher-level tools:** `yum`, `dnf`, and `apt`.
- **Repository installation:** The package manager finds packages and resolves dependencies.
- **Local installation:** Provide the correct path to the downloaded `.rpm` or `.deb` file.
- **Verification:** Use package queries and command checks to confirm installation status.