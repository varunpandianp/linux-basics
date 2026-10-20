# Linux Administration Notes: SSH, SCP, iptables and Cron Jobs

> **Goal:** Learn how to connect to remote servers, copy files securely, control network traffic using firewall rules, and schedule automated tasks in Linux.

---

# 1. SSH — Secure Shell

## What is SSH?

SSH (Secure Shell) is a network protocol used to securely connect to and manage a remote Linux server.

**Real-world example:** A DevOps engineer uses SSH from a laptop to access an AWS EC2 instance and troubleshoot a web application.

### SSH connection syntax

```bash
ssh username@hostname
```

Example:

```bash
ssh bob@devapp01
```

This connects as user `bob` to the server `devapp01`.

### Common SSH commands

| Command | Purpose |
|---|---|
| `ssh bob@devapp01` | Connect to a remote server |
| `ssh bob@10.244.221.22` | Connect using an IP address |
| `ssh -p 2222 bob@devapp01` | Connect using a custom SSH port |
| `ssh -v bob@devapp01` | Troubleshoot SSH connection and authentication |
| `ssh -i ~/.ssh/id_rsa bob@devapp01` | Specify a private key |
| `exit` | Close the SSH session |

### SSH key authentication

SSH key authentication uses a key pair:

- **Private key:** Kept secret on the client machine.
- **Public key:** Installed on the remote server.

Common key files:

```text
~/.ssh/id_rsa       # Private key
~/.ssh/id_rsa.pub   # Public key
```

Generate an RSA key pair:

```bash
ssh-keygen -t rsa -b 4096
```

Install the public key for Bob on the target server:

```bash
ssh-copy-id bob@devapp01
```

Test the connection:

```bash
ssh bob@devapp01
```

The public key is normally added to:

```text
/home/bob/.ssh/authorized_keys
```

The private key must never be copied to the remote server.

### SSH troubleshooting

Check whether the SSH service is running on the server:

```bash
sudo systemctl status sshd
```

On Ubuntu/Debian, the service may be named `ssh`:

```bash
sudo systemctl status ssh
```

Check whether the server listens on port 22:

```bash
sudo ss -lntp | grep ':22'
```

Test connectivity from the client:

```bash
ssh -v bob@devapp01
```

Common causes of failure:

- Incorrect hostname or IP address.
- DNS resolution failure.
- Port 22 blocked by a firewall or security group.
- SSH service stopped.
- Incorrect username or private-key permissions.
- Public key missing from `authorized_keys`.

Protect a private key with restrictive permissions:

```bash
chmod 600 ~/.ssh/id_rsa
```

---

# 2. SCP — Secure Copy

## What is SCP?

`scp` copies files securely between machines using SSH.

### Syntax

```bash
scp source destination
```

### Copy a local file to a remote server

```bash
scp /home/bob/caleston-code.tar.gz bob@devapp01:/home/bob/
```

Meaning:

- Source: `/home/bob/caleston-code.tar.gz` on the local machine.
- Destination user: `bob`.
- Destination server: `devapp01`.
- Destination directory: `/home/bob/`.

### Copy a remote file to the local machine

```bash
scp bob@devapp01:/home/bob/report.txt /home/bob/
```

### Copy a directory recursively

```bash
scp -r /home/bob/project bob@devapp01:/home/bob/
```

### Use a custom SSH port

```bash
scp -P 2222 report.txt bob@devapp01:/home/bob/
```

**Note:** `scp` uses uppercase `-P` for the port, while `ssh` uses lowercase `-p`.

### Verify the copied file

```bash
ssh bob@devapp01
ls -lh /home/bob/caleston-code.tar.gz
```

### SSH vs SCP

| Command | Purpose |
|---|---|
| `ssh` | Remote login and command execution |
| `scp` | Secure file copying |
| `ssh-copy-id` | Install a public SSH key on a remote account |

---

# 3. iptables — Linux Firewall

## What is iptables?

`iptables` is a command-line tool for configuring IPv4 firewall rules in Linux.

It controls which network packets are allowed, rejected, or dropped.

**Real-world example:** Allow a developer to access a web server through SSH and HTTP while blocking unnecessary outbound web traffic.

### Check iptables

```bash
sudo iptables -L -n -v
```

Display rules with line numbers:

```bash
sudo iptables -L -n -v --line-numbers
```

Check version:

```bash
iptables --version
```

> Modern Linux systems may use `nftables`, `firewalld`, or `ufw`. Check the existing firewall before changing rules. Do not flush rules blindly.

## 3.1 The three main chains

| Chain | Meaning | Example |
|---|---|---|
| `INPUT` | Traffic entering the local machine | SSH connection arriving at a server |
| `OUTPUT` | Traffic leaving the local machine | Application server connecting to a database |
| `FORWARD` | Traffic passing through the machine | Linux router forwarding packets |

Remember:

- `INPUT` = entering this machine.
- `OUTPUT` = leaving this machine.
- `FORWARD` = passing through this machine.

## 3.2 The three common rule actions

| Action | Meaning | Behavior |
|---|---|---|
| `ACCEPT` | Allow the matching packet | Packet continues through the network stack |
| `DROP` | Silently discard the packet | Sender normally receives no explicit firewall rejection |
| `REJECT` | Refuse the packet | Sender receives an error or rejection response |

`REJECT` often returns an ICMP error for general traffic or a TCP reset for TCP traffic, depending on the rule and options.

## 3.3 Understand iptables options

Example:

```bash
sudo iptables -A INPUT -p tcp -s 10.244.221.31 --dport 22 -j ACCEPT
```

| Option | Meaning |
|---|---|
| `-A INPUT` | Append a rule to the INPUT chain |
| `-I INPUT` | Insert a rule, at the beginning by default |
| `-D INPUT` | Delete a rule or a rule by its number |
| `-p tcp` | Match TCP packets |
| `-s` | Specify a source IP address or network |
| `-d` | Specify a destination IP address or network |
| `--sport` | Match source port |
| `--dport` | Match destination port |
| `-j ACCEPT` | Allow matching packets |
| `-j DROP` | Drop matching packets |
| `-j REJECT` | Reject matching packets |
| `-n` | Display numeric addresses and ports |
| `-v` | Show verbose information |
| `-L` | List rules |
| `-F` | Flush rules in a chain or table; use with caution |

## 3.4 Allow SSH from a specific laptop

Suppose Bob's laptop IP address is `10.244.221.31`.

```bash
sudo iptables -A INPUT -p tcp -s 10.244.221.31 --dport 22 -j ACCEPT
```

This allows matching incoming TCP traffic from that IP to the server's SSH port.

## 3.5 Allow HTTP from a specific laptop

```bash
sudo iptables -A INPUT -p tcp -s 10.244.221.31 --dport 80 -j ACCEPT
```

This permits matching HTTP traffic from that source IP.

## 3.6 Allow HTTPS from a specific laptop

```bash
sudo iptables -A INPUT -p tcp -s 10.244.221.31 --dport 443 -j ACCEPT
```

This allows matching HTTPS traffic.

## 3.7 Allow outgoing access to a repository

Suppose the repository server IP is `10.244.221.22`.

```bash
sudo iptables -I OUTPUT -p tcp -d 10.244.221.22 --dport 80 -j ACCEPT
```

This inserts an outbound allow rule for TCP port 80 to that IP address.

**Important:** iptables rules match IP addresses, not continuously updated hostnames. Resolve a hostname to an IP first, and remember that DNS answers can change.

## 3.8 Allow database connections

PostgreSQL commonly listens on TCP port `5432`.

```bash
sudo iptables -A OUTPUT -p tcp -d 10.244.221.4 --dport 5432 -j ACCEPT
```

This allows matching outbound TCP packets to `10.244.221.4:5432`, provided earlier rules do not block them.

## 3.9 Block outgoing HTTP and HTTPS

```bash
sudo iptables -A OUTPUT -p tcp --dport 80 -j DROP
sudo iptables -A OUTPUT -p tcp --dport 443 -j DROP
```

These rules drop outgoing TCP packets to destination ports 80 and 443 **only if the packets reach those rules**.

### Rule order matters

For example:

```bash
sudo iptables -I OUTPUT -p tcp -d 10.244.221.22 --dport 80 -j ACCEPT
sudo iptables -A OUTPUT -p tcp --dport 80 -j DROP
```

The first rule allows HTTP to the specified repository IP. The later rule drops other matching outbound HTTP traffic that reaches it.

`-I` inserts a rule near the top; `-A` appends a rule at the end.

## 3.10 Understand request and response traffic

Example: the application server connects to the database server.

1. The application server sends a database request — `OUTPUT` on the application server.
2. The database receives the request — `INPUT` on the database server.
3. The database sends the response — `OUTPUT` on the database server.
4. The application server receives the response — `INPUT` on the application server.

A common stateful firewall rule is:

```bash
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```

This accepts incoming packets belonging to established or related connections, subject to the rest of the firewall configuration.

It is a general example, not a replacement for inspecting the existing firewall policy.

## 3.11 Check and remove rules

List numbered rules:

```bash
sudo iptables -L INPUT -n -v --line-numbers
```

Delete a rule by its line number:

```bash
sudo iptables -D INPUT 2
```

Use the actual rule number shown on your system. Line numbers can change after rules are inserted or deleted.

Delete a matching rule by specification:

```bash
sudo iptables -D INPUT -p tcp -s 10.244.221.31 --dport 22 -j ACCEPT
```

Check packet counters:

```bash
sudo iptables -L -n -v
```

Counters can help establish whether traffic is matching a rule.

### Important safety notes

- An `ACCEPT` rule may not help if an earlier rule has already dropped the packet.
- Allowing a port through a firewall does not start the service listening on that port.
- Host firewalls, cloud security groups, network ACLs, and routing must all be considered.
- Commands added directly to iptables may not survive reboot unless rules are saved using the system's supported persistence mechanism.
- Test changes carefully, especially on a remote server, to avoid locking yourself out of SSH.

---

# 4. Cron Jobs — Task Scheduling

## What is cron?

Cron is a Linux service that runs scheduled commands or scripts automatically at specified times.

Examples include:

- Running a backup every night.
- Cleaning temporary files weekly.
- Checking a service every five minutes.
- Running a report on the first day of each month.

## 4.1 Inspect cron jobs

List the current user's jobs:

```bash
crontab -l
```

Edit the current user's jobs:

```bash
crontab -e
```

List Bob's jobs:

```bash
sudo crontab -u bob -l
```

Edit Bob's jobs:

```bash
sudo crontab -u bob -e
```

Count non-empty, non-comment entries in Bob's personal crontab:

```bash
sudo crontab -u bob -l 2>/dev/null | grep -Ev '^[[:space:]]*(#|$)' | wc -l
```

This counts crontab entries, not necessarily every system-wide task that might run as Bob.

## 4.2 Cron schedule format

A standard user crontab entry has five time fields followed by a command.

```text
minute hour day-of-month month day-of-week command
```

Example:

```cron
0 6 1 * * /usr/local/bin/last-reboot.sh
```

This runs the script at 6:00 AM on the first day of every month.

### Meaning of the fields

| Field | Allowed values | Meaning |
|---|---|---|
| Minute | `0–59` | Minute of the hour |
| Hour | `0–23` | Hour of the day |
| Day of month | `1–31` | Calendar day |
| Month | `1–12` | Month |
| Day of week | `0–7` | Day of week; `0` and `7` commonly mean Sunday |

Common symbols:

- `*` = every valid value.
- `,` = list of values.
- `-` = range.
- `/` = step interval.

## 4.3 Common cron examples

| Requirement | Cron expression |
|---|---|
| Every minute | `* * * * *` |
| Every five minutes | `*/5 * * * *` |
| Every hour | `0 * * * *` |
| Every day at midnight | `0 0 * * *` |
| Every day at 6 AM | `0 6 * * *` |
| Every Sunday at 8 AM | `0 8 * * 0` |
| First day of every month at 6 AM | `0 6 1 * *` |
| Every 15 minutes from 9 AM to 5 PM | `*/15 9-17 * * *` |

**Note:** Cron day-of-month and day-of-week behavior can be surprising when both fields are restricted. Follow the target system's cron implementation and lab requirements.

## 4.4 Schedule the monthly reboot-history script

Requirement:

> Run `/usr/local/bin/last-reboot.sh` on the first day of every month at 6 AM.

Edit the intended user's crontab:

```bash
crontab -e
```

Add:

```cron
0 6 1 * * /usr/local/bin/last-reboot.sh
```

If the task specifically requires Bob's crontab:

```bash
sudo crontab -u bob -e
```

Then add the same entry.

Verify:

```bash
crontab -l
```

Or for Bob:

```bash
sudo crontab -u bob -l
```

Make sure the script exists:

```bash
ls -l /usr/local/bin/last-reboot.sh
```

If it needs to be executable:

```bash
sudo chmod +x /usr/local/bin/last-reboot.sh
```

Cron jobs run with a limited environment. Use absolute paths in scheduled commands and scripts, and ensure the selected account has the permissions required to execute the script.

## 4.5 Redirect cron output to a log

Example:

```cron
0 6 1 * * /usr/local/bin/last-reboot.sh >> /home/bob/last-reboot.log 2>&1
```

- `>>` appends standard output to the log.
- `2>&1` sends standard error to the same destination.

Ensure the account running the job can write to that log file.

## 4.6 Where are cron jobs stored?

| Location | Purpose |
|---|---|
| User crontab | Jobs configured for an individual account |
| `/etc/crontab` | System-wide cron configuration, including a user field |
| `/etc/cron.d/` | Additional system cron definitions |
| `/etc/cron.hourly/` | Scripts scheduled by the system's hourly cron mechanism |
| `/etc/cron.daily/` | Daily scripts |
| `/etc/cron.weekly/` | Weekly scripts |
| `/etc/cron.monthly/` | Monthly scripts |

User crontabs are commonly stored under `/var/spool/cron/` or `/var/spool/cron/crontabs/`, depending on the distribution. Use `crontab` commands rather than editing spool files directly.

## 4.7 Troubleshoot cron

Check the cron service:

```bash
sudo systemctl status cron
```

On many RHEL/CentOS systems, the service is named `crond`:

```bash
sudo systemctl status crond
```

Inspect logs where supported:

```bash
sudo journalctl -u cron
sudo journalctl -u crond
```

Depending on the distribution, cron logs may instead be written to `/var/log/syslog`, `/var/log/cron`, or another configured log.

Common causes of failure:

- Incorrect cron schedule.
- Wrong script path.
- Missing execute permission.
- Incorrect script interpreter.
- Missing environment variables or PATH entries.
- Incorrect file permissions.
- Cron daemon not running.
- Script runs but fails because it assumes an interactive shell.

---

# 5. Real-world DevOps scenario

Imagine a small environment:

- Bob's laptop: administrator workstation.
- `devapp01`: web application server.
- `devdb01`: database server.
- `caleston-repo-01`: software repository.

Requirements:

1. Bob can SSH into the web and database servers.
2. Bob can access the web server over HTTP.
3. The application server can reach the repository over HTTP.
4. The application can reach the database on PostgreSQL's port.
5. A maintenance script runs monthly.

### SSH checks

From Bob's laptop:

```bash
ssh bob@devapp01
ssh bob@devdb01
```

### HTTP checks

From Bob's laptop:

```bash
curl -I http://devapp01
```

From the application server:

```bash
curl -I http://caleston-repo-01
```

### Firewall inspection

On the relevant server:

```bash
sudo iptables -L -n -v --line-numbers
```

### Scheduled task verification

```bash
sudo crontab -u bob -l
```

### General troubleshooting sequence

1. Verify hostname resolution.
2. Check IP addresses and routes.
3. Check the destination service is listening.
4. Inspect firewall and network policy.
5. Test the connection with SSH, curl, or an appropriate port test.
6. Inspect logs and counters.
7. Apply the smallest necessary change.
8. Test again from the original source.

---

# 6. Quick Revision Cheat Sheet

## SSH and SCP

```bash
ssh bob@devapp01
ssh-copy-id bob@devapp01
scp file.txt bob@devapp01:/home/bob/
```

## iptables

```bash
sudo iptables -L -n -v --line-numbers
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A OUTPUT -p tcp --dport 443 -j DROP
```

## Cron

```bash
crontab -l
crontab -e
sudo crontab -u bob -l
```

Monthly job:

```cron
0 6 1 * * /usr/local/bin/last-reboot.sh
```

## Final mental model

- **SSH:** Securely access a remote server.
- **SCP:** Securely copy files between machines.
- **iptables:** Filter packets according to firewall rules.
- **Cron:** Run commands automatically on a schedule.

These four topics are foundational for Linux administration, production troubleshooting, and DevOps operations.
