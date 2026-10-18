# Working with the Linux Shell — Study Notes

> **Learning path:** Linux Fundamentals → Shell Commands → Environment Variables → Bash Configuration → DevOps Automation  
> **Purpose:** Understand how the Linux shell works, execute commands confidently, navigate the filesystem, manage files, use help documentation, and configure a persistent working environment.

---

## Table of Contents

1. What Is a Shell?
2. Why Do We Need a Shell?
3. Types of Linux Shells
4. Terminal vs Shell
5. Home Directory and Linux Directories
6. Linux Command Structure
7. Commands Without Arguments
8. Internal (Built-in) vs External Commands
9. The `type` Command
10. Basic Linux Commands
11. Absolute Path vs Relative Path
12. Directory Navigation: `cd`, `pushd`, `popd`, `dirs`
13. File Operations: Create, Copy, Move, Rename, Delete
14. Reading and Editing Files: `cat`, `touch`, `vi`
15. Pagers: `more` and `less`
16. Command-Line Help: `help`, `whatis`, `man`, `--help`, `apropos`
17. Shell Variables and Environment Variables
18. The `export` Command
19. The `PATH` Environment Variable
20. Aliases
21. Command History
22. Persistent Configuration: `.profile`, `.bashrc`, and `.bash_profile`
23. Hands-on Practice Lab
24. Common Mistakes and Troubleshooting
25. Linux in DevOps
26. Interview Questions
27. Learning Log

---

# 1. What Is a Shell?

A **shell** is a program that provides a command-line interface for interacting with an operating system.

It accepts commands, interprets them, and runs programs.

For example:

```bash
pwd
ls
date
uptime
```

The shell reads each command and arranges for the appropriate operation to be performed.

## How the shell works

```text
User
 |
 v
Terminal
 |
 v
Shell (Bash)
 |
 v
Command or Program
 |
 v
Operating System / Kernel
 |
 v
Hardware Resources
```

Example:

When you run `ls`, the shell identifies how to execute the command. The `ls` program requests filesystem information through operating system interfaces and displays the directory contents.

**Important:** The shell is not the Linux kernel. The shell is a user-space program that interacts with the operating system.

# 2. Why Do We Need a Shell?

The shell allows us to operate and automate a Linux system.

Main uses:

- Execute Linux commands.
- Navigate files and directories.
- Create, copy, move, and delete files.
- Manage processes and services.
- Configure environment variables.
- Run scripts and automate repetitive work.
- Connect to remote servers using SSH.
- Troubleshoot networking and applications.
- Run commands in CI/CD pipelines.
- Administer cloud servers and container environments.

## Why is the shell important in DevOps?

DevOps engineers frequently use shells to:

- Execute build and deployment scripts.
- Install software on Linux servers.
- Configure application environments.
- Inspect logs during production incidents.
- Run Git and cloud CLI commands.
- Automate routine system administration.
- Execute commands inside Docker containers.
- Troubleshoot Kubernetes nodes and workloads.

Example:

```bash
#!/usr/bin/env bash

echo "Starting deployment"
date
uptime
echo "Deployment script completed"
```

This is a simple shell script containing commands that can be executed together.

# 3. Types of Linux Shells

Different shells provide different syntax, features, and configuration behavior.

| Shell | Description | Common use |
|---|---|---|
| `sh` | Traditional Bourne-compatible shell interface | Portable shell scripts |
| `bash` | Bourne Again Shell | Linux administration and scripting |
| `zsh` | Z Shell | Interactive command-line customization |
| `ksh` | KornShell | Unix administration and scripting |
| `csh` | C Shell | Historical Unix shell |
| `tcsh` | Enhanced C Shell | Interactive command-line use |
| `fish` | Friendly Interactive Shell | User-friendly interactive shell |

## Bash

Bash is widely used in Linux administration and DevOps automation.

Check the Bash version:

```bash
bash --version
```

Check whether Bash is available:

```bash
command -v bash
```

## Check the current shell

```bash
echo "$SHELL"
```

Example output:

```text
/bin/bash
```

`$SHELL` usually represents the user's configured login shell. It does not always identify the shell currently executing commands.

To identify the current process more directly:

```bash
ps -p "$$" -o comm=
```

## Check the configured login shell

```bash
getent passwd "$USER"
```

Example output:

```text
varun:x:1000:1000:Varun:/home/varun:/bin/bash
```

The final field is the configured login shell.

# 4. Terminal vs Shell

These two terms are related but different.

| Terminal | Shell |
|---|---|
| Provides the interface for typing commands and viewing output | Interprets commands and starts programs |
| Can be a physical terminal or terminal emulator | Can be Bash, Zsh, or another shell |
| Displays command input and output | Processes shell syntax and command execution |

Example:

You open a terminal application on Ubuntu. It starts Bash. You type `pwd`, and Bash executes the command.

# 5. Home Directory and Linux Directories

## What is the home directory?

The home directory is the personal directory assigned to a user.

For a user named `bob`, it might be:

```text
/home/bob
```

For the root user, it is commonly:

```text
/root
```

The exact home path can vary.

## Why do we need a home directory?

The home directory is used to store:

- Personal files and folders.
- Learning notes and scripts.
- SSH configuration.
- Shell startup files.
- User-specific application settings.
- Command history and other user configuration.

Common configuration files include:

```text
~/.profile
~/.bashrc
~/.bash_profile
~/.bash_history
```

Not every file exists by default, and the shell does not read every file in every session.

## Useful home directory commands

```bash
pwd
echo "$HOME"
cd ~
cd
ls -la ~
```

| Symbol or variable | Meaning |
|---|---|
| `~` | Current user's home directory in shell path expansion |
| `$HOME` | Environment variable containing the home directory |
| `.` | Current directory |
| `..` | Parent directory |
| `/` | Filesystem root |

## Linux directory hierarchy

| Directory | Purpose |
|---|---|
| `/` | Root of the filesystem |
| `/home` | Home directories for regular users |
| `/root` | Root user's home directory |
| `/etc` | System-wide configuration |
| `/var` | Variable data, including logs and caches |
| `/tmp` | Temporary files |
| `/usr` | Applications, libraries, and shared resources |
| `/bin` | Common commands; often linked to `/usr/bin` |
| `/sbin` | System administration commands; often linked to `/usr/sbin` |
| `/dev` | Device files |
| `/proc` | Virtual filesystem containing process and kernel information |
| `/sys` | Kernel and device information |
| `/boot` | Boot-related files |
| `/mnt` | Common temporary mount point |
| `/media` | Common mount point for removable media |
| `/opt` | Optional application software |

# 6. Linux Command Structure

A command commonly follows this structure:

```text
command [options] [arguments]
```

Example:

```bash
ls -l /home
```

| Component | Example | Meaning |
|---|---|---|
| Command | `ls` | Lists directory contents |
| Option | `-l` | Displays a detailed listing |
| Argument | `/home` | Specifies the directory to inspect |

Another example:

```bash
date "+%Y-%m-%d"
```

- `date` is the command.
- `"+%Y-%m-%d"` is an argument specifying the output format.

Options and arguments are not the same thing. Options modify a command's behavior, while arguments commonly identify the object or input on which the command operates. Some commands use options that themselves require arguments.

## Example with multiple arguments

```bash
mkdir project logs scripts
```

Here, `mkdir` creates three directories.

## Command with an option and argument

```bash
head -n 5 file.txt
```

- `head` is the command.
- `-n` specifies the number of lines.
- `5` is the value for that option.
- `file.txt` is the input file.

# 7. Commands Without Arguments

Some commands work without any arguments.

## Example: `echo`

```bash
echo
```

Output:

```text

```

With an argument:

```bash
echo Hello
```

Output:

```text
Hello
```

With multiple words:

```bash
echo Hello Linux World
```

Output:

```text
Hello Linux World
```

`echo` prints its arguments followed by a newline by default. With no arguments, it prints a newline.

## Example: `date`

```bash
date
```

Displays the current system date and time.

## Example: `uptime`

```bash
uptime
```

Displays how long the system has been running, the number of logged-in users, and system load averages.

## Example: `pwd`

```bash
pwd
```

Displays the current working directory.

**Learning point:** A command may work with no arguments, with one argument, or with several arguments depending on its behavior.

# 8. Internal (Built-in) vs External Commands

Linux shells can execute built-in commands and external programs.

## 8.1 Shell built-ins

A built-in command is implemented inside the shell itself.

Examples in Bash:

- `cd`
- `echo`
- `pwd`
- `alias`
- `export`
- `history`
- `type`
- `read`
- `umask`

Check a built-in:

```bash
type cd
```

Typical output:

```text
cd is a shell builtin
```

### Why must `cd` be a built-in?

The shell maintains its own current working directory.

If `cd` ran only as a separate child process, that child could change its own directory, but it could not change the parent shell's working directory.

Therefore, shells normally implement `cd` as a built-in.

## 8.2 External commands

External commands are executable programs found on the filesystem.

Examples commonly include:

- `mv`
- `date`
- `uptime`
- `cp`
- `mkdir`
- `cat`
- `touch`
- `grep`

Check an external command:

```bash
type date
```

Possible output:

```text
date is /usr/bin/date
```

The actual path depends on the distribution and system configuration.

## 8.3 Important distinction

Some commands, such as `echo`, `pwd`, and `mkdir`, may be available both as shell built-ins or through external executables, depending on the command and environment.

A command name does not always map to exactly one implementation.

## 8.4 Compare commands

```bash
type echo
type cd
type pwd
type mkdir
type mv
type date
type uptime
type cp
```

This helps identify how Bash resolves each command name.

# 9. The `type` Command

The `type` command identifies how the shell interprets a command name.

Syntax:

```bash
type COMMAND
```

Examples:

```bash
type echo
type cd
type pwd
type mkdir
type date
type uptime
```

Possible results include:

```text
cd is a shell builtin
date is /usr/bin/date
```

## What about `type hello`?

Run:

```bash
type hello
```

If `hello` is not defined as a function, alias, built-in, or executable in the command search path, Bash may report:

```text
bash: type: hello: not found
```

This is expected when the command does not exist in the shell's command-resolution environment.

## Related commands

```bash
command -v date
which date
type -a echo
```

- `command -v`: Reports how a command name would be resolved.
- `which`: Commonly finds executable paths, but behavior depends on its implementation.
- `type -a`: Displays all matching definitions or command resolutions known to the shell.

For shell troubleshooting, `type` and `command -v` are usually good first choices.

# 10. Basic Linux Commands

| Command | Purpose | Example |
|---|---|---|
| `pwd` | Show current directory | `pwd` |
| `ls` | List files | `ls -la` |
| `cd` | Change directory | `cd /etc` |
| `mkdir` | Create directories | `mkdir project` |
| `touch` | Create an empty file or update timestamps | `touch notes.txt` |
| `cp` | Copy files | `cp a.txt b.txt` |
| `mv` | Move or rename files | `mv old.txt new.txt` |
| `rm` | Remove files | `rm test.txt` |
| `cat` | Display or concatenate file contents | `cat notes.txt` |
| `date` | Display date and time | `date` |
| `uptime` | Show system uptime and load averages | `uptime` |
| `echo` | Print text or expanded variable values | `echo "$HOME"` |
| `whoami` | Show effective username | `whoami` |
| `hostname` | Show hostname | `hostname` |
| `uname` | Display kernel/system information | `uname -a` |
| `clear` | Clear terminal display | `clear` |

## Command practice

```bash
pwd
ls -la
date
uptime
whoami
hostname
uname -r
```

# 11. Absolute Path vs Relative Path

A path specifies the location of a file or directory.

## 11.1 Absolute path

An absolute path begins at the filesystem root `/`.

Example:

```bash
cd /home/bob
```

Another example:

```bash
cat /etc/os-release
```

An absolute path does not depend on the current working directory.

## 11.2 Relative path

A relative path is interpreted from the current working directory.

Suppose the current directory is:

```text
/home/bob
```

Command:

```bash
cd Documents
```

This attempts to enter:

```text
/home/bob/Documents
```

Another example:

```bash
cd ..
```

Moves to the parent directory.

## 11.3 Comparison

| Absolute path | Relative path |
|---|---|
| Starts from `/` | Starts from the current directory |
| Example: `/home/bob/file.txt` | Example: `Documents/file.txt` |
| Independent of current directory | Depends on current directory |
| Useful in scripts requiring a specific location | Useful for working within a project |

## 11.4 Special path symbols

| Symbol | Meaning |
|---|---|
| `/` | Filesystem root |
| `.` | Current directory |
| `..` | Parent directory |
| `~` | Current user's home directory |
| `-` | Previous working directory when used with `cd -` |

# 12. Directory Navigation: `cd`, `pushd`, `popd`, `dirs`

## 12.1 `cd`

Changes the current working directory.

```bash
cd /etc
cd /var/log
cd ..
cd ~
cd -
```

`cd -` switches to the previous working directory and usually prints the directory it switched to.

## 12.2 `pushd`

`pushd` changes directories while saving directory information on a directory stack.

Example:

```bash
cd ~
pushd /etc
```

This changes to `/etc` and records the previous directory on the stack.

## 12.3 `popd`

`popd` removes a directory from the stack and typically changes to the new top directory.

Example:

```bash
popd
```

If you have not established a suitable stack entry, `popd` may report an error.

## 12.4 `dirs`

Displays the directory stack.

```bash
dirs
dirs -v
```

## 12.5 Why use `pushd` and `popd`?

Imagine you are working in a project directory but temporarily need to inspect `/etc`.

```bash
cd ~/project
pushd /etc
pwd
popd
pwd
```

You can inspect the system directory and return to the saved directory without manually typing the original path.

**Note:** `pushd`, `popd`, and `dirs` are Bash built-ins and are also available in some other shells.

# 13. File Operations: Create, Copy, Move, Rename, Delete

## 13.1 Create a directory

```bash
mkdir project
mkdir logs scripts
mkdir -p project/src/scripts
```

`mkdir -p` creates missing parent directories and does not report an error merely because the target directories already exist.

## 13.2 Create a file

```bash
touch notes.txt
```

If the file does not exist, `touch` creates an empty file. If it exists, `touch` normally updates its timestamps without deleting its contents.

## 13.3 Copy a file

```bash
cp source.txt backup.txt
```

Copy into a directory:

```bash
cp notes.txt /tmp/
```

Copy a directory recursively:

```bash
cp -r project project-backup
```

Use care when copying to an existing destination because files may be overwritten.

## 13.4 Rename a file

```bash
mv oldname.txt newname.txt
```

The `mv` command is used for both renaming and moving.

## 13.5 Move a file

```bash
mv notes.txt /tmp/
```

Move a file into a directory and give it a new name:

```bash
mv notes.txt /tmp/linux-notes.txt
```

## 13.6 Delete a file

```bash
rm test.txt
```

Delete an empty directory:

```bash
rmdir empty-directory
```

Delete a directory and its contents:

```bash
rm -r project-backup
```

**Safety warning:** `rm` normally does not move files to a recycle bin. Recursive deletion can permanently remove data. Verify the target path before using `rm -r`, and be especially cautious with `rm -rf`.

# 14. Reading and Editing Files: `cat`, `touch`, `vi`

## 14.1 `cat`

Displays file contents.

```bash
cat notes.txt
```

Display multiple files:

```bash
cat file1.txt file2.txt
```

Create a short file using shell redirection:

```bash
echo "Linux learning notes" > notes.txt
```

Append a line:

```bash
echo "Working with shell" >> notes.txt
```

`>` overwrites a file; `>>` appends to it.

## 14.2 `touch`

```bash
touch file.txt
```

Useful for creating empty files during labs.

## 14.3 `vi`

`vi` is a text editor available on many Unix-like systems. It may be provided by Vim or another compatible implementation.

Open a file:

```bash
vi notes.txt
```

### Essential `vi` commands

| Key or command | Action |
|---|---|
| `i` | Enter insert mode |
| `Esc` | Return to normal mode |
| `:w` | Save changes |
| `:q` | Quit |
| `:wq` | Save and quit |
| `:q!` | Quit without saving changes |
| `dd` | Delete the current line in normal mode |
| `yy` | Copy the current line in normal mode |
| `p` | Paste after the cursor in normal mode |

Basic workflow:

1. Open the file using `vi notes.txt`.
2. Press `i` to insert text.
3. Type your content.
4. Press `Esc`.
5. Type `:wq`.
6. Press Enter.

If you want to exit without saving, press `Esc`, type `:q!`, and press Enter.

# 15. Pagers: `more` and `less`

Pagers display long text a screenful at a time.

## 15.1 `more`

```bash
more /etc/services
```

Useful keys commonly include:

- `Space`: Move forward one screen.
- `Enter`: Move forward one line.
- `q`: Quit.

## 15.2 `less`

```bash
less /var/log/syslog
```

`less` generally provides more navigation and searching features than `more`.

Common keys:

| Key | Action |
|---|---|
| `Space` | Move forward |
| `b` | Move backward |
| `/error` | Search forward for `error` |
| `?error` | Search backward for `error` |
| `n` | Repeat the previous search |
| `N` | Repeat the search in the opposite direction |
| `g` | Go to the beginning |
| `G` | Go to the end |
| `q` | Quit |

The log filename may differ by distribution. If `/var/log/syslog` does not exist, inspect available logs or use `journalctl`.

# 16. Command-Line Help

Linux provides several ways to learn how a command works.

## 16.1 `--help`

Many external commands support:

```bash
date --help
cp --help
mkdir --help
```

This usually displays a short usage summary and common options.

Not every command implements `--help` in the same way.

## 16.2 `man`

`man` opens the manual page for a command.

```bash
man date
man cp
man mkdir
```

Navigate with the pager keys and press `q` to quit.

Search within a manual page by typing `/pattern`, then press Enter.

## 16.3 `whatis`

Displays a brief description of a command based on the manual-page database.

```bash
whatis date
whatis uptime
```

If no description appears, the manual database may need updating or the command may lack a corresponding manual entry.

## 16.4 `apropos`

Searches manual-page descriptions for a keyword.

```bash
apropos date
apropos network
apropos copy
```

It is useful when you know what you want to do but do not know the command name.

## 16.5 `help`

Bash's `help` command describes shell built-ins.

```bash
help
help cd
help echo
help export
```

For external programs, use `man`, `--help`, or `info` when available.

## 16.6 `type`

```bash
type cd
type date
type -a echo
```

This helps identify whether a command is a built-in, alias, function, or external executable.

## Help command comparison

| Command | Purpose |
|---|---|
| `help cd` | Help for a Bash built-in |
| `man date` | Detailed manual page |
| `date --help` | Short usage information, if supported |
| `whatis date` | Brief manual description |
| `apropos network` | Search manual descriptions |
| `type date` | Identify command resolution |

# 17. Shell Variables and Environment Variables

Variables store values that can be used by commands and scripts.

## 17.1 Shell variable

```bash
PROJECT=MERCURY
echo "$PROJECT"
```

Output:

```text
MERCURY
```

There must be no spaces around `=` when assigning a shell variable.

Incorrect:

```bash
PROJECT = MERCURY
```

This is interpreted as a command invocation rather than a variable assignment.

## 17.2 Environment variable

An environment variable is a name-value pair passed to child processes when they are started.

Example:

```bash
export PROJECT=MERCURY
```

Verify:

```bash
echo "$PROJECT"
printenv PROJECT
```

Both should display:

```text
MERCURY
```

## 17.3 Shell variable vs environment variable

| Shell variable | Environment variable |
|---|---|
| Exists in the current shell | Is exported for child processes |
| Not automatically inherited by child processes | Inherited by child processes, unless modified or removed |
| Can be exported later | Can be created using `export` |

Example:

```bash
PROJECT=MERCURY
bash -c 'echo "${PROJECT-unset}"'
```

If `PROJECT` was not exported, the child Bash process will normally display:

```text
unset
```

Now export it:

```bash
export PROJECT
bash -c 'echo "$PROJECT"'
```

Expected output:

```text
MERCURY
```

## 17.4 Common environment variables

| Variable | Purpose |
|---|---|
| `HOME` | User's home directory |
| `USER` | Commonly contains the login username |
| `SHELL` | Usually contains the configured login shell path |
| `PATH` | Directories searched for executable commands |
| `PWD` | Current directory maintained by the shell |
| `OLDPWD` | Previous working directory maintained by the shell |
| `TERM` | Terminal type description |
| `LANG` | Locale setting |
| `EDITOR` | Preferred editor used by some programs |

Inspect variables:

```bash
echo "$HOME"
echo "$USER"
echo "$SHELL"
echo "$PATH"
echo "$TERM"
env
printenv
```

`env` and `printenv` commonly display environment variables, whereas `set` in Bash displays shell variables, functions, and other shell state.

# 18. The `export` Command

The `export` built-in marks a shell variable for inclusion in the environment of subsequently started child processes.

Example:

```bash
PROJECT=MERCURY
export PROJECT
```

Equivalent form:

```bash
export PROJECT=MERCURY
```

Check the result:

```bash
printenv PROJECT
```

Expected output:

```text
MERCURY
```

## Remove an environment variable

```bash
unset PROJECT
```

This removes the variable from the current shell environment if it is set there. It does not automatically remove a corresponding line from a profile file.

# 19. The `PATH` Environment Variable

`PATH` is a colon-separated list of directories that the shell searches when resolving executable command names.

Example:

```text
/usr/local/bin:/usr/bin:/bin
```

When you run:

```bash
date
```

Bash searches for an appropriate executable in the directories listed in `PATH`, unless another command resolution such as an alias or function takes precedence.

## 19.1 Inspect PATH

```bash
echo "$PATH"
```

Display one directory per line:

```bash
printf '%s\n' "$PATH" | tr ':' '\n'
```

## 19.2 Add a directory temporarily

```bash
export PATH="$PATH:$HOME/bin"
```

This appends `$HOME/bin` to `PATH` in the current shell and its subsequently started child processes.

If the directory contains executable scripts, they may now be run by name.

## 19.3 Make PATH persistent

Add the configuration to the appropriate startup file for your shell:

```bash
echo 'export PATH="$PATH:$HOME/bin"' >> ~/.profile
```

Reload the file in the current compatible shell:

```bash
source ~/.profile
```

Check:

```bash
echo "$PATH"
```

**Important:** Repeatedly appending the same line creates duplicates. Inspect the profile before adding the entry, and ensure the target directory is trusted. Adding an untrusted directory to `PATH` can cause the wrong executable to run.

# 20. Aliases

An alias creates a shortcut for a command in shells that support aliases.

Example:

```bash
alias ll='ls -l'
```

Now run:

```bash
ll
```

This expands to:

```bash
ls -l
```

## Create another alias

```bash
alias up='uptime'
```

Run:

```bash
up
```

## View aliases

```bash
alias
alias ll
```

## Remove an alias

```bash
unalias ll
```

## Make an alias persistent

For a Bash interactive environment, add it to `~/.bashrc`:

```bash
echo "alias ll='ls -l'" >> ~/.bashrc
```

Reload:

```bash
source ~/.bashrc
```

In some lab exercises, the instructions specifically require aliases in `~/.profile`. Follow the lab's requested file when that file is part of validation.

**Note:** An alias is not a standalone executable. It is interpreted by the shell, and aliases generally are not expanded in non-interactive Bash scripts unless alias expansion is explicitly enabled.

# 21. Command History

The shell history records commands entered in an interactive session.

Display history:

```bash
history
```

Search recent commands:

```bash
history | tail
```

Run a previous command by its history number:

```bash
!NUMBER
```

Replace `NUMBER` with the actual history entry number.

Search interactively in Bash:

- Press `Ctrl+R`.
- Type part of the previous command.
- Press `Ctrl+R` again to find another match.
- Press Enter to execute the selected command.

The history file for Bash is commonly:

```text
~/.bash_history
```

History behavior depends on shell settings and when history is written to disk.

**Security warning:** Command history can contain sensitive information. Avoid placing passwords, tokens, or secrets directly in command arguments.

# 22. Persistent Configuration: `.profile`, `.bashrc`, and `.bash_profile`

## 22.1 What does persistent mean?

A temporary shell variable normally disappears when the shell session ends.

A persistent configuration stores the assignment or command in a startup file that the relevant shell reads in a future session.

Example:

```bash
export PROJECT=MERCURY
```

Temporary assignment:

```bash
export PROJECT=MERCURY
```

Persistent assignment:

```bash
echo 'export PROJECT=MERCURY' >> ~/.profile
```

The second command appends the assignment to a file, so it can be loaded during future login sessions that read that file.

## 22.2 Common startup files

| File | Typical purpose |
|---|---|
| `~/.profile` | User login-shell configuration, commonly used for environment settings |
| `~/.bash_profile` | Bash login-shell configuration, if present |
| `~/.bash_login` | Another Bash login startup file, used under Bash's startup rules |
| `~/.bashrc` | Interactive non-login Bash configuration |
| `~/.bash_history` | Common Bash history file |

## 22.3 Bash startup behavior

For a Bash login shell, Bash normally reads the first existing readable file among:

1. `~/.bash_profile`
2. `~/.bash_login`
3. `~/.profile`

For an interactive non-login Bash shell, Bash normally reads `~/.bashrc`.

A `~/.bash_profile` commonly sources `~/.bashrc`, but that behavior depends on its contents.

This distinction explains why a variable or alias might work in one terminal session but not another.

## 22.4 Add a persistent environment variable

Suppose the task is:

> Create `PROJECT=MERCURY` and make it persistent in `~/.profile`.

Command:

```bash
echo 'export PROJECT=MERCURY' >> ~/.profile
```

Load it into the current shell:

```bash
source ~/.profile
```

Verify:

```bash
echo "$PROJECT"
```

Expected output:

```text
MERCURY
```

## 22.5 Add a persistent alias

Suppose the task is:

> Create an alias named `up` for `uptime` and store it in `~/.profile`.

Command:

```bash
echo "alias up='uptime'" >> ~/.profile
```

Reload the file in a compatible interactive Bash session:

```bash
source ~/.profile
```

Test:

```bash
up
```

If the alias does not work in the current shell, check whether you are using an interactive shell and whether that shell loads aliases from the specified file.

## 22.6 Change the prompt persistently

Bash uses `PS1` for its primary interactive prompt.

Example:

```bash
export PS1='[\D{%a %b %d}]\u@\h:\w\$ '
```

Prompt components:

| Component | Meaning |
|---|---|
| `\D{%a %b %d}` | Date, such as `Sun Oct 18` |
| `\u` | Username |
| `\h` | Short hostname |
| `\w` | Current working directory |
| `\$` | `$` for regular users or `#` for root |

To persist the prompt for an interactive Bash environment, place the assignment in a startup file that the relevant Bash session reads, commonly `~/.bashrc`.

Some lab exercises specifically validate `~/.profile`. Follow their stated requirements, and ensure the file is read by the shell session you are testing.

## 22.7 Inspect startup files

```bash
ls -la ~
cat ~/.profile
cat ~/.bashrc
```

A file may not exist on every system.

Before appending a configuration line, check whether it is already present to avoid duplicates.

# 23. Hands-on Practice Lab

Complete these exercises in your Linux practice VM or KodeKloud lab.

## Lab 1: Identify the shell

```bash
echo "$SHELL"
ps -p "$$" -o comm=
cat /etc/shells
```

Questions:

- What is your configured login shell?
- What shell is executing your current commands?
- Which shells are listed as valid login shells?

## Lab 2: Identify command types

```bash
type echo
type cd
type pwd
type mkdir
type mv
type date
type uptime
type cp
type hello
```

Record which commands are built-ins and which resolve to external executables. If `hello` is unavailable, explain why `type` reports that it cannot find it.

## Lab 3: Practice absolute and relative paths

```bash
pwd
cd /tmp
pwd
mkdir shell-lab
cd shell-lab
pwd
cd ..
pwd
```

Explain the difference between the absolute path `/tmp/shell-lab` and the relative path `shell-lab`.

## Lab 4: Use the directory stack

```bash
cd ~
pushd /tmp
dirs
popd
pwd
```

Record your working directory before and after `pushd` and `popd`.

## Lab 5: Practice file operations

```bash
mkdir -p ~/shell-lab
cd ~/shell-lab
touch notes.txt
echo "Linux shell practice" > notes.txt
cat notes.txt
cp notes.txt backup.txt
mv backup.txt renamed.txt
ls -l
```

Inspect the results. Clean up the practice files after completing the exercise if they are no longer needed.

## Lab 6: Use help commands

```bash
type cd
help cd
man date
date --help
whatis date
apropos network
```

Some manual-page tools or databases may not be installed or configured in a minimal environment.

## Lab 7: Create an environment variable

```bash
export PROJECT=MERCURY
echo "$PROJECT"
printenv PROJECT
```

Explain why exporting the variable allows child processes to inherit it.

## Lab 8: Configure PATH

```bash
mkdir -p "$HOME/bin"
export PATH="$PATH:$HOME/bin"
echo "$PATH"
```

Explain why the change is temporary unless you add the configuration to an appropriate startup file.

## Lab 9: Create an alias

```bash
alias up='uptime'
up
```

Persist the alias in the startup file specified by your lab.

## Lab 10: Persistent configuration

Add the following to `~/.profile`:

```bash
export PROJECT=MERCURY
```

Reload and verify:

```bash
source ~/.profile
echo "$PROJECT"
```

Then inspect the file:

```bash
cat ~/.profile
```

Record the change and explain the difference between the file containing the assignment and the variable being loaded in your current session.

---

# 24. Common Mistakes and Troubleshooting

## Mistake 1: Spaces around variable assignment

Incorrect:

```bash
PROJECT = MERCURY
```

Correct:

```bash
PROJECT=MERCURY
```

## Mistake 2: Forgetting to export

```bash
PROJECT=MERCURY
```

This creates a shell variable, but a child process does not automatically inherit it.

Correct:

```bash
export PROJECT=MERCURY
```

## Mistake 3: Using `>` instead of `>>`

```bash
echo 'export PROJECT=MERCURY' > ~/.profile
```

This overwrites the file contents.

To append a line without replacing existing content:

```bash
echo 'export PROJECT=MERCURY' >> ~/.profile
```

## Mistake 4: Assuming a persistent file updates the current shell automatically

Appending a line to a profile file does not automatically apply it to the already-running shell.

Use a compatible reload command:

```bash
source ~/.profile
```

Or open a new session that reads the relevant startup file.

## Mistake 5: Confusing `HOME` and `/root`

`$HOME` represents the home directory for the current user.

For a regular user, it might be `/home/bob`; for root, it is commonly `/root`.

Check:

```bash
echo "$HOME"
whoami
```

## Mistake 6: Assuming all commands are external

Commands such as `cd`, `export`, and `alias` are Bash built-ins.

Check with:

```bash
type cd
type export
type alias
```

## Mistake 7: Assuming every shell reads `.profile`

Different shells and session types load different startup files.

If a variable or alias is missing, check the shell, the startup file, and whether the file is actually read by that session.

---

# 25. Linux in DevOps

Understanding the shell is essential for DevOps automation.

| DevOps task | Shell skills used |
|---|---|
| Git operations | Commands, paths, environment variables |
| CI/CD pipelines | Scripts, exit codes, pipes, redirection |
| Docker administration | Shell commands, processes, networking |
| Kubernetes troubleshooting | CLI commands, logs, environment variables |
| Cloud VM administration | SSH, filesystem navigation, permissions |
| Application deployment | Variables, scripts, file operations |
| Monitoring | `uptime`, `top`, log inspection |
| Incident response | `grep`, `less`, `journalctl`, process tools |

## Example: A basic system information script

Create a file named `system-info.sh`:

```bash
#!/usr/bin/env bash
set -euo pipefail

echo "User: $(whoami)"
echo "Home: $HOME"
echo "Shell: $SHELL"
echo "Hostname: $(hostname)"
echo "Current directory: $(pwd)"
echo "Date: $(date)"
echo "Uptime:"
uptime
```

Run:

```bash
chmod +x system-info.sh
./system-info.sh
```

This script demonstrates how shell commands, variables, and command substitution can be combined into a repeatable task.

---

# 26. Interview Questions

## Shell fundamentals

1. What is a shell?
2. What is the difference between a terminal and a shell?
3. What is Bash?
4. What are the common types of Linux shells?
5. What is the difference between a shell variable and an environment variable?
6. What is the purpose of a home directory?

## Commands and paths

7. What is the difference between a command, option, and argument?
8. What is the difference between an absolute path and a relative path?
9. What is the difference between `cp` and `mv`?
10. What is the difference between `>` and `>>`?
11. What is the difference between `more` and `less`?
12. What is the purpose of `pushd` and `popd`?

## Command resolution and help

13. What is the difference between shell built-ins and external commands?
14. Why is `cd` a built-in?
15. How do you determine the type of a command?
16. What is the difference between `help`, `man`, `whatis`, and `apropos`?
17. What happens when you run `type hello` if the command does not exist?

## Environment and persistence

18. What does `export` do?
19. What is the `PATH` environment variable?
20. How do you make an environment variable persistent?
21. What is the difference between `.profile` and `.bashrc`?
22. What is an alias?
23. What is `PS1`?
24. Why might an alias work in one terminal but not another?

## DevOps application

25. Why is shell scripting useful in CI/CD pipelines?
26. How can you debug a shell script?
27. Why are environment variables useful in deployment automation?
28. How would you make a deployment script executable?
29. Why should secrets not be printed into shell history or CI/CD logs?
30. How would you troubleshoot a command that works manually but fails in automation?

---

# 27. Learning Log

## Working with Shell — Study Session

**Date:** YYYY-MM-DD

**Course:** KodeKloud — Learning Linux Basics Course & Labs

**Topics studied:**
- Shell fundamentals.
- Shell types.
- Linux directories and home directory.
- Command structure and command types.
- Absolute and relative paths.
- File operations.
- Help commands.
- Environment variables.
- Aliases and history.
- Persistent shell configuration.

**Commands practiced:**

```bash
pwd
echo "$SHELL"
type cd
type date
pushd /tmp
popd
man date
export PROJECT=MERCURY
echo "$PATH"
history
```

**What I understood:**
- 

**What confused me:**
- 

**Errors encountered:**
- 

**How I solved them:**
- 

**DevOps use case:**
- 

**Interview questions to revise:**
- 

**Next session goals:**
- 

---

## Final Revision Checklist

- [ ] Explain what a shell does.
- [ ] Identify the current shell.
- [ ] Explain the purpose of the home directory.
- [ ] Identify built-in and external commands using `type`.
- [ ] Understand command arguments and options.
- [ ] Navigate using absolute and relative paths.
- [ ] Use `cd`, `pushd`, and `popd`.
- [ ] Create, copy, rename, move, and delete practice files safely.
- [ ] Read and edit files using `cat`, `less`, and `vi`.
- [ ] Find command documentation.
- [ ] Explain shell variables and environment variables.
- [ ] Use `export` correctly.
- [ ] Understand and update `PATH` safely.
- [ ] Create aliases and inspect command history.
- [ ] Explain `.profile`, `.bashrc`, and login-shell behavior.
- [ ] Write and execute a simple Bash script.

**Key takeaway:** The Linux shell is more than a command prompt. It is an interface for operating systems, managing configuration, and automating infrastructure. These skills form a foundation for Git, CI/CD, Docker, Kubernetes, cloud administration, and SRE troubleshooting.
