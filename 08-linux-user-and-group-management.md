# Linux User and Group Management — Complete Notes

> **Goal:** Understand Linux users, groups, UID, GID, account files, passwords, `sudo`, `su`, and the commands used to manage user accounts.

## 1. What is a Linux user account?

A Linux user account identifies who is accessing a system. Linux uses user and group IDs to control access to files, directories, processes, and administrative operations.

For example, when `john` creates a file, Linux records the owner's UID and the file's group GID.

```bash
id john
```

Example output:

```text
uid=1010(john) gid=1010(john) groups=1010(john)
```

- **UID** — User ID; identifies the user.
- **GID** — Group ID; identifies a group.
- **Group name** — Human-readable name for the group.
- **Primary group** — Default group associated with the user.
- **Supplementary groups** — Additional groups that grant group-based access.

## 2. Types of Linux accounts

| Account type | Purpose | Example |
|---|---|---|
| Superuser | Full administrative privileges | `root`, UID `0` |
| Regular user | Normal human login and work | `bob`, `john` |
| System account | Runs operating-system services and background tasks | `daemon`, `nobody` |
| Service account | Runs a particular application or service | `nginx`, `postgres` |

**Important:** System and service accounts are often configured without an interactive login shell. However, the exact configuration varies by distribution and application.

### Root / superuser

- Username: `root`
- UID: `0`
- Has broad administrative control over the system.
- Can modify protected files, manage users, install software, and control services.

Check:

```bash
id root
```

### Regular users

Regular users usually have non-zero UIDs. They can access files according to their permissions and use `sudo` only if authorized.

```bash
id bob
```

### System and service accounts

These accounts allow services to run with a specific identity instead of running everything as root.

For example, a web server might run as `nginx` or `www-data`.

```bash
getent passwd nginx
getent passwd www-data
```

A missing result simply means that particular account may not exist on your system.

**Best practice:** Give each service only the permissions it needs.

---

## 3. UID, GID, primary group and supplementary groups

### UID — User ID

A numeric identifier assigned to a user.

```bash
id -u john
```

Example:

```text
1010
```

### GID — Group ID

A numeric identifier assigned to a group.

```bash
id -g john
```

Example:

```text
1010
```

### Primary group

Every user has one primary group. Files created by that user normally inherit the user's primary group, subject to directory settings and other filesystem rules.

```bash
id -gn john
```

Example:

```text
john
```

### Supplementary groups

A user may belong to additional groups.

```bash
id -G john
id -nG john
```

- `id -G` — Displays numeric group IDs.
- `id -nG` — Displays group names.

### Can the UID and GID be the same number?

**Yes.** A common configuration is to create a private primary group with the same name and numeric ID as the user.

Example:

```text
Username: john
UID:      1010
Group:    john
GID:      1010
```

But Linux does not require matching UID and GID numbers. The UID identifies a user, while the GID identifies a group. They are separate ID spaces.

Check an existing ID before assigning it:

```bash
getent passwd 1010
getent group 1010
```

If either command returns an entry, that numeric ID is already associated with an account or group.

---

## 4. Important Linux account files

| File | Purpose | Stores password hashes? |
|---|---|---|
| `/etc/passwd` | User account information | No |
| `/etc/shadow` | Password hashes and password-aging information | Yes |
| `/etc/group` | Group names, GIDs and supplementary membership lists | No |
| `/etc/gshadow` | Protected group administration and group-password information | May contain group password hashes |

These files are essential for traditional local Linux account management.

### A. `/etc/passwd`

View account entries:

```bash
cat /etc/passwd
```

For a particular user:

```bash
getent passwd john
```

Example entry:

```text
john:x:1010:1010:John:/home/john:/bin/sh
```

#### Breakdown of `/etc/passwd`

The fields are separated by colons (`:`).

| Field | Value | Meaning |
|---|---|---|
| 1 | `john` | Username |
| 2 | `x` | Password information is held in `/etc/shadow` on a typical shadow-password system |
| 3 | `1010` | UID |
| 4 | `1010` | Primary GID |
| 5 | `John` | GECOS/comment field |
| 6 | `/home/john` | Home directory |
| 7 | `/bin/sh` | Login shell |

**Remember the order:**

`username : password marker : UID : GID : comment : home directory : shell`

### B. `/etc/shadow`

This file stores password hashes and password-aging fields.

```bash
sudo getent shadow john
```

Or, on a local system:

```bash
sudo grep '^john:' /etc/shadow
```

Example format (illustrative only):

```text
john:$y$j9T$EXAMPLE_HASH:20000:0:99999:7:::
```

#### Breakdown of `/etc/shadow`

| Field | Meaning |
|---|---|
| 1 | Username |
| 2 | Password hash or a marker indicating a password state |
| 3 | Last password-change day, measured from the Unix epoch |
| 4 | Minimum days before another password change |
| 5 | Maximum password age |
| 6 | Warning days before expiry |
| 7 | Inactive days after password expiry before disabling the account |
| 8 | Account expiration day |
| 9 | Reserved field |

**Security:** Do not share real `/etc/shadow` output. Password hashes are sensitive and can be attacked offline. Use `sudo` only when necessary.

Set a user's password with:

```bash
sudo passwd john
```

Lock or unlock password authentication:

```bash
sudo passwd -l john
sudo passwd -u john
```

A password lock does not necessarily disable every authentication method or terminate existing sessions. For full account deactivation, understand the account's other authentication methods and running processes.

### C. `/etc/group`

View group entries:

```bash
cat /etc/group
```

Find a particular group:

```bash
getent group john
```

Example:

```text
john:x:1010:bob,alice
```

#### Breakdown of `/etc/group`

| Field | Value | Meaning |
|---|---|---|
| 1 | `john` | Group name |
| 2 | `x` | Group-password information is stored separately on systems using `gshadow` |
| 3 | `1010` | GID |
| 4 | `bob,alice` | Supplementary members listed for this group |

**Important:** The fourth field lists supplementary group members. A user's primary group relationship is recorded by the user's GID in `/etc/passwd`; the user does not have to appear in the fourth field of their primary group's entry.

### D. `/etc/gshadow`

This file stores protected group administration information.

```bash
sudo getent gshadow john
```

Not all NSS configurations expose it through `getent`; you may instead inspect the local file with appropriate privileges.

Avoid manually editing account files with ordinary text editors. Use commands such as `useradd`, `usermod`, `groupadd`, and `gpasswd`, or use the safe editors `vipw` and `vigr` when manual editing is necessary.

---

## 5. `su` vs `sudo`

### `su` — Switch user

Switch to another user:

```bash
su john
```

Start a login shell as `john`:

```bash
su - john
```

Switch to root, if permitted and the required authentication succeeds:

```bash
su -
```

Return to the previous shell:

```bash
exit
```

The hyphen in `su - john` loads a login-style environment for the target user, including their home directory and environment setup.

### `sudo` — Run a command with authorized privileges

Run one command as root:

```bash
sudo systemctl status nginx
```

Check your own sudo privileges:

```bash
sudo -l
```

Check another user's sudo privileges (requires sufficient administrative privileges):

```bash
sudo -l -U bob
```

### Difference

| Command | Main purpose |
|---|---|
| `su john` | Switch to user `john` |
| `su - john` | Switch to `john` with a login environment |
| `sudo command` | Run an authorized command with elevated privileges |
| `sudo -i` | Start an interactive root login shell, if authorized |
| `sudo -l` | List the current user's sudo permissions |

**Best practice:** Use `sudo` for specific administrative tasks rather than remaining in a root shell unnecessarily.

---

## 6. Basic commands to identify users and sessions

| Command | What it does |
|---|---|
| `whoami` | Displays the effective username |
| `id` | Displays current UID, primary GID and groups |
| `id john` | Displays identity information for `john` |
| `who` | Shows users currently logged in to recorded sessions |
| `w` | Shows logged-in users and their activity |
| `last` | Shows login history from the system's login records |
| `users` | Lists usernames associated with current login sessions |
| `groups` | Shows group membership |
| `getent passwd john` | Looks up user account information |
| `getent group developers` | Looks up a group |
| `passwd` | Changes the current user's password |
| `sudo -l` | Lists the current user's sudo privileges |

Examples:

```bash
whoami
id
who
w
last
groups
```

`who`, `w`, and `last` provide different views of login activity; they are not guaranteed to show every kind of session or authentication event.

---

## 7. Create a user account

### Example: Create `john` with UID 1010, primary group `john`, and shell `/bin/sh`

First, check whether the username, UID, and GID are already in use:

```bash
getent passwd john
getent passwd 1010
getent group john
getent group 1010
```

If they are available, create a group with GID `1010` and then create the user:

```bash
sudo groupadd -g 1010 john
sudo useradd -m -u 1010 -g john -s /bin/sh john
```

This produces the intended configuration:

- Username: `john`
- UID: `1010`
- Primary group: `john`
- GID: `1010`
- Home directory: `/home/john`
- Login shell: `/bin/sh`

If the group already exists with another GID, do not blindly run `groupadd` again. Inspect the existing group and decide whether it should be reused or whether a different ID is required.

Set a password:

```bash
sudo passwd john
```

Verify the result:

```bash
id john
getent passwd john
getent group john
```

### Create a user with automatically assigned IDs

On many Linux distributions, `useradd` without explicit UID/GID options assigns available IDs according to the system's configuration.

```bash
sudo useradd -m alice
sudo passwd alice
```

---

## 8. Create and manage groups

### Create a group

```bash
sudo groupadd developers
```

### Create a group with a specific GID

```bash
sudo groupadd -g 2000 developers
```

### Check a group

```bash
getent group developers
getent group 2000
```

### Add a user to a supplementary group

```bash
sudo usermod -aG developers john
```

- `-a` means append.
- `-G` specifies supplementary groups.

**Important:** Always use `-aG` when adding a supplementary group with `usermod`. Omitting `-a` can replace the user's existing supplementary group list.

Verify:

```bash
id john
groups john
```

The new group membership is normally applied to new login sessions. The user may need to log out and log back in.

### Change the primary group

```bash
sudo usermod -g developers john
```

This makes `developers` the primary group for future operations by `john`. Review ownership and access implications before changing a primary group.

### Remove a user from a supplementary group

On systems with a compatible `gpasswd` implementation:

```bash
sudo gpasswd -d john developers
```

Alternatively, carefully use `usermod -G` with the **complete list** of supplementary groups that should remain. Do not omit groups unintentionally.

### Assign a group password

```bash
sudo gpasswd developers
```

This prompts an administrator to set a group password.

Group passwords are rarely needed in modern administration. Prefer managing membership explicitly with `usermod -aG` or `gpasswd -a`:

```bash
sudo gpasswd -a john developers
```

### Remove a group

```bash
sudo groupdel developers
```

Do not remove a group while it is still needed as a user's primary group. Check its members and associated files first.

---

## 9. Modify and delete users

### Modify the login shell

```bash
sudo usermod -s /bin/sh john
```

### Change the home directory

```bash
sudo usermod -d /home/john-new -m john
```

The `-m` option moves the contents of the existing home directory to the new location, when applicable.

### Lock or unlock password authentication

```bash
sudo passwd -l john
sudo passwd -u john
```

### Delete a user but keep the home directory

```bash
sudo userdel john
```

### Delete a user and their home directory

```bash
sudo userdel -r john
```

**Warning:** `userdel -r` can remove the user's home directory and mail spool. Check for files owned by the user's UID elsewhere on the system before deleting an account.

Find files owned by UID 1010:

```bash
sudo find / -xdev -uid 1010 -ls 2>/dev/null
```

`-xdev` prevents traversal into other mounted filesystems. Run additional searches on other filesystems if needed.

---

## 10. Password aging and account expiry

View password-aging information:

```bash
sudo chage -l john
```

Set a maximum password age of 90 days:

```bash
sudo chage -M 90 john
```

Require a password change at the next login:

```bash
sudo chage -d 0 john
```

Set an account expiration date:

```bash
sudo chage -E 2027-12-31 john
```

Password expiry and account expiry are different. Use the policy required by your organization or lab.

---

## 11. Common lab tasks — quick solutions

| Requirement | Command |
|---|---|
| Find a user's UID | `id -u john` |
| Find the user's primary GID | `id -g john` |
| Find the primary group name | `id -gn john` |
| Show all group names | `id -nG john` |
| Find the login shell | `getent passwd john` |
| Create a group with GID 2000 | `sudo groupadd -g 2000 developers` |
| Create a user with UID 1010 | `sudo useradd -u 1010 john` |
| Create a user with home and shell | `sudo useradd -m -s /bin/sh john` |
| Set the primary group | `sudo usermod -g developers john` |
| Add a supplementary group | `sudo usermod -aG developers john` |
| Set a password | `sudo passwd john` |
| Check sudo permissions | `sudo -l -U john` |
| List login history | `last` |
| Delete a group | `sudo groupdel developers` |

These commands are examples. Check for existing usernames and IDs, and satisfy the lab's exact requirements before executing them.

---

## 12. Troubleshooting common mistakes

### Error: `UID already in use`

Check the numeric UID:

```bash
getent passwd 1010
```

Choose another unused UID or investigate the existing account.

### Error: `GID already in use`

Check the GID:

```bash
getent group 1010
```

Choose an unused GID or use the intended existing group.

### Error: `group 'john' does not exist`

Create the group first:

```bash
sudo groupadd john
```

Then create the user with `-g john`.

### User is missing a new group membership

Verify:

```bash
id john
getent group developers
```

Log out and back in to refresh the user's group membership in the session.

### User cannot use `sudo`

Check:

```bash
sudo -l -U john
id john
```

Inspect the sudoers rules with `visudo` or the appropriate files in `/etc/sudoers.d/`. Membership in a group named `sudo` or `wheel` alone does not guarantee permission.

---

## 13. Final mental model

```text
User account
    |
    +-- UID --------------------> Identifies the user
    |
    +-- Primary GID ------------> Identifies the user's primary group
    |
    +-- Supplementary groups ---> Additional group-based access
    |
    +-- /etc/passwd ------------> Account identity and login fields
    |
    +-- /etc/shadow ------------> Password hashes and aging
    |
    +-- /etc/group -------------> Group names, IDs and listed members
    |
    +-- /etc/gshadow -----------> Protected group administration
    |
    +-- sudo -------------------> Authorized elevated commands
    |
    +-- su ---------------------> Switch to another user
```

### Five commands to remember first

```bash
id john
getent passwd john
getent group john
sudo usermod -aG developers john
sudo -l -U john
```

If you understand these commands and the four account files, you have a solid foundation for Linux user and group management in DevOps.
