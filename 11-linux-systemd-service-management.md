# Linux Service Management with systemd — Complete Notes

> **Goal:** Learn how Linux starts, stops, manages, and monitors services using `systemd`, `systemctl`, and `journalctl`.

## 1. What is systemd?

`systemd` is a system and service manager used by many modern Linux distributions, including Ubuntu, Debian, RHEL, CentOS, and Fedora.

It is responsible for starting system services, managing dependencies, controlling boot targets, and collecting service logs through its journal.

Examples of services:
- `sshd` — remote SSH access
- `nginx` — web server
- `httpd` — Apache web server on many RHEL-based systems
- `cron` or `crond` — scheduled tasks
- `docker` — container runtime service

Check whether systemd is running as PID 1:

```bash
ps -p 1 -o pid,comm,args
```

Check the systemd version:

```bash
systemctl --version
```

## 2. Important systemd terminology

| Term | Meaning |
|---|---|
| `systemd` | System and service manager |
| Unit | A resource managed by systemd |
| Service unit | Defines how a service is managed |
| `systemctl` | Command to control systemd units |
| `journalctl` | Command to inspect systemd journal logs |
| Daemon | A background process that provides a service |
| Unit file | Configuration describing a unit |
| Target | A group of units used to reach a system state |

Systemd supports several unit types:

| Extension | Purpose | Example |
|---|---|---|
| `.service` | Services and daemons | `nginx.service` |
| `.target` | Groups of units or system states | `multi-user.target` |
| `.socket` | Socket-based activation | `docker.socket` |
| `.timer` | Scheduled activation | `backup.timer` |
| `.mount` | Filesystem mounts | `home.mount` |
| `.path` | Path-change activation | `watch-folder.path` |

## 3. What is a service unit file?

A systemd service unit file tells systemd how to start, stop, restart, and manage a service.

Common locations:

| Location | Purpose |
|---|---|
| `/etc/systemd/system/` | Administrator-created or customized units |
| `/usr/lib/systemd/system/` | Vendor-installed unit files on many RHEL-based systems |
| `/lib/systemd/system/` | Vendor unit files on some Debian-based systems |
| `/run/systemd/system/` | Runtime unit files |

To inspect the unit file used by a service:

```bash
systemctl cat sshd
```

Or, on a Debian-based system:

```bash
systemctl cat ssh
```

## 4. Create a custom systemd service

### Real-world scenario

Suppose you have a shell script at:

```text
/usr/local/bin/hello-service.sh
```

You want systemd to start it and manage its lifecycle.

### Step 1 — Create the script

```bash
sudo vi /usr/local/bin/hello-service.sh
```

Add:

```bash
#!/bin/bash
echo "Hello from systemd service"
```

Make it executable:

```bash
sudo chmod +x /usr/local/bin/hello-service.sh
```

**Important:** This script exits immediately after printing its message. Systemd can execute it as a one-shot task, but it is not a long-running daemon.

### Step 2 — Create the service unit file

```bash
sudo vi /etc/systemd/system/hello-service.service
```

Add:

```ini
[Unit]
Description=Hello Example Service
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/hello-service.sh
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

### Step 3 — Understand each section

#### `[Unit]`

```ini
[Unit]
Description=Hello Example Service
After=network.target
```

- `Description` — human-readable service description.
- `After` — ordering dependency; it does not itself pull in or start the named unit.

#### `[Service]`

```ini
[Service]
Type=oneshot
ExecStart=/usr/local/bin/hello-service.sh
RemainAfterExit=yes
```

- `Type=oneshot` — the command performs a task and exits.
- `ExecStart` — command executed when the service starts.
- `RemainAfterExit=yes` — systemd considers the unit active after the command exits successfully.

#### `[Install]`

```ini
[Install]
WantedBy=multi-user.target
```

- `WantedBy` defines the target that should want the service when it is enabled.
- This section is used by `systemctl enable`; it does not automatically start the service.

### Step 4 — Reload systemd configuration

```bash
sudo systemctl daemon-reload
```

**Why?** You created a new unit file. `daemon-reload` makes systemd reread unit files and rebuild its dependency information.

It does not restart all services or automatically start your new service.

### Step 5 — Start the service

```bash
sudo systemctl start hello-service
```

### Step 6 — Check the status

```bash
systemctl status hello-service
```

For a successful `Type=oneshot` unit with `RemainAfterExit=yes`, the state can show `active (exited)`.

### Step 7 — Enable it at boot

```bash
sudo systemctl enable hello-service
```

To enable and start it immediately:

```bash
sudo systemctl enable --now hello-service
```

Because this is a one-shot script, it will run at startup but will not remain as a continuously running process.

## 5. Example of a long-running service

A typical web server or worker process remains running. Its unit may look like this:

```ini
[Unit]
Description=Example Worker Service
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/worker.sh
Restart=on-failure
RestartSec=5
User=worker
Group=worker

[Install]
WantedBy=multi-user.target
```

This is an illustrative unit. Before using it, ensure that:
- `/usr/local/bin/worker.sh` exists and runs in the foreground.
- The `worker` user and group exist.
- The worker has the required file permissions.
- The command supports running as a service.

`Restart=on-failure` tells systemd to attempt a restart when the service exits unsuccessfully, subject to systemd's restart limits and other configuration.

## 6. systemctl — Manage service state

`systemctl` controls systemd units.

### Common service commands

| Command | Purpose |
|---|---|
| `systemctl status nginx` | Check service state and recent logs |
| `sudo systemctl start nginx` | Start a service now |
| `sudo systemctl stop nginx` | Stop a service now |
| `sudo systemctl restart nginx` | Stop and start a service |
| `sudo systemctl reload nginx` | Ask the service to reload its configuration, if supported |
| `sudo systemctl enable nginx` | Configure the service to start at boot |
| `sudo systemctl disable nginx` | Remove its boot-time enablement |
| `sudo systemctl enable --now nginx` | Enable at boot and start now |
| `sudo systemctl disable --now nginx` | Disable at boot and stop now |
| `sudo systemctl daemon-reload` | Reload unit-file definitions |
| `systemctl is-active nginx` | Check whether the service is active |
| `systemctl is-enabled nginx` | Check whether it is enabled at boot |

**Remember the difference:**

- `start` and `stop` change the current runtime state.
- `enable` and `disable` change boot-time activation.
- `restart` restarts the service.
- `reload` asks the service to reload configuration without a full restart, if supported.
- `daemon-reload` reloads systemd's unit-file configuration; it is not the same as reloading an application's configuration.

## 7. Unit states and listing units

List currently loaded units:

```bash
systemctl list-units
```

List all loaded service units, including inactive ones:

```bash
systemctl list-units --type=service --all
```

List installed service unit files and their enablement state:

```bash
systemctl list-unit-files --type=service
```

Check a particular service:

```bash
systemctl is-active sshd
systemctl is-enabled sshd
```

Common states include:

| State | Meaning |
|---|---|
| `active (running)` | Service is running |
| `active (exited)` | Task completed, but the unit remains active by configuration |
| `inactive` | Unit is not currently active |
| `failed` | Unit failed to start or encountered a failure |
| `enabled` | Configured for activation through its install links |
| `disabled` | Not enabled through those install links |

A service may be `active` but `disabled`; it is running now but is not configured to start automatically through the usual boot enablement mechanism.

## 8. journalctl — Read system logs

`journalctl` reads logs collected by the systemd journal.

### Common commands

| Command | Purpose |
|---|---|
| `journalctl` | Show journal entries |
| `journalctl -b` | Show logs from the current boot |
| `journalctl -b -1` | Show logs from the previous boot, if retained |
| `journalctl -u nginx` | Show logs for a service |
| `journalctl -u nginx -b` | Show service logs from the current boot |
| `journalctl -f` | Follow new log entries |
| `journalctl -n 50` | Show the latest 50 entries |
| `journalctl -p err` | Show error-priority entries |
| `journalctl --since today` | Show logs since the start of today |
| `journalctl -xe` | Show recent journal entries with explanatory context |

### Real-time troubleshooting

Follow Nginx logs:

```bash
sudo journalctl -u nginx -f
```

Show recent Nginx logs:

```bash
sudo journalctl -u nginx -n 50
```

Show error-level messages from the current boot:

```bash
sudo journalctl -b -p err
```

If the journal does not retain logs across reboots, historical entries may not be available.

## 9. Real-world scenario: Web server is down

Suppose the website hosted on `devapp01` is not responding.

### Step 1 — Check the service state

```bash
sudo systemctl status nginx
```

### Step 2 — Inspect service logs

```bash
sudo journalctl -u nginx -n 100 --no-pager
```

### Step 3 — Validate the Nginx configuration

```bash
sudo nginx -t
```

### Step 4 — Restart only after identifying and correcting the issue

```bash
sudo systemctl restart nginx
```

### Step 5 — Verify status and listening ports

```bash
systemctl status nginx
sudo ss -lntp | grep ':80'
```

### Step 6 — Test HTTP locally

```bash
curl -I http://localhost
```

If the service is active but the website is still unreachable, continue checking firewall rules, routing, DNS, virtual-host configuration, application health, and cloud security policies.

## 10. systemd targets and boot modes

A target groups units together to represent a system state.

Common targets:

| Target | Purpose |
|---|---|
| `poweroff.target` | Shut down the system |
| `rescue.target` | Minimal rescue environment |
| `multi-user.target` | Multi-user system, generally without a graphical desktop |
| `graphical.target` | Multi-user system with a graphical login environment |
| `reboot.target` | Reboot the system |

### Check the current default target

```bash
systemctl get-default
```

Example output:

```text
multi-user.target
```

### Change the default target to multi-user mode

```bash
sudo systemctl set-default multi-user.target
```

Verify:

```bash
systemctl get-default
```

This changes the default target used on subsequent boots. It does not immediately switch the running system into that target.

### Change the target for the current session

```bash
sudo systemctl isolate multi-user.target
```

**Caution:** `isolate` can stop units that are not part of the target and may terminate a graphical session. Use it carefully, especially on remote or production systems.

### Set graphical mode as the default

```bash
sudo systemctl set-default graphical.target
```

## 11. Boot and service troubleshooting checklist

When a service fails:

1. Check the unit status.
   ```bash
   systemctl status service-name
   ```

2. Inspect service logs.
   ```bash
   sudo journalctl -u service-name -n 100 --no-pager
   ```

3. Check whether the unit file is valid and loaded.
   ```bash
   systemctl cat service-name
   sudo systemctl daemon-reload
   ```

4. Check the application's configuration with its own validation command.

5. Check file ownership, permissions, ports and dependencies.

6. Start or restart the service only after addressing the root cause.

7. Verify the service and test the application from the relevant client.

Do not assume every service failure can be solved by restarting it. Logs and configuration validation usually provide the evidence needed to identify the problem.

## 12. Quick revision cheat sheet

```bash
# Service status and state
systemctl status nginx
systemctl is-active nginx
systemctl is-enabled nginx

# Runtime control
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx

# Boot-time configuration
sudo systemctl enable nginx
sudo systemctl disable nginx
sudo systemctl enable --now nginx

# Unit configuration
systemctl cat nginx
sudo systemctl daemon-reload
systemctl list-units --type=service --all
systemctl list-unit-files --type=service

# Logs
sudo journalctl -u nginx -n 50
sudo journalctl -u nginx -f
sudo journalctl -b -p err

# Boot targets
systemctl get-default
sudo systemctl set-default multi-user.target
sudo systemctl isolate multi-user.target
```

## Final mental model

- **systemd** manages services and system states.
- **Unit files** define how services should be managed.
- **systemctl** controls services and targets.
- **daemon-reload** makes systemd reread unit definitions.
- **journalctl** helps diagnose service and boot failures.
- **Targets** group units to reach a particular system state.

These skills are essential for Linux administration, web-server troubleshooting, and DevOps operations.
