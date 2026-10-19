Linux DevOps Troubleshooting Scenario: vp-cloud Website Is Down
Your scenario is: from your laptop, you try to access the website vp-cloud, but the website is unreachable. The server may be down, but we must not assume that immediately.
As a DevOps engineer, troubleshoot from the laptop toward the server, identify the failing layer, fix the actual issue, and verify that the website works.
1. Understand the end-to-end connection

1. Laptop / Browser
   URL, browser, local network

2. DNS / Name Resolution
   Can vp-cloud be resolved to an IP?

3. Network / Routing
   IP, gateway, route, firewall

4. Server Reachability
   Is the server running and reachable?

5. Port 80 / 443
   HTTP / HTTPS listening and allowed?

6. Web Server
   Nginx / Apache service and configuration

7. Website
   Application, content, logs, successful response

# Scenario-Based Troubleshooting: `vp-cloud` Website Is Down

## Scenario

From my laptop, I cannot access the website named `vp-cloud`. The website may be unreachable because of DNS, network routing, a firewall, a stopped server, or a failed web server.

**Goal:** Identify the root cause, restore the website, and verify access from the laptop.

> Replace example IP addresses, interface names, domain names, and service names with the actual values from your lab. The examples assume a Linux laptop or server and may need adjustment for your environment.

---

## Step 1: Check the Browser and URL

First, confirm that the URL is correct.

Examples:

```text
http://vp-cloud
https://vp-cloud
http://vp-cloud.example.com
```

Check:
- Is the hostname correct?
- Is the protocol HTTP or HTTPS?
- Is the website expected to use port 80 or 443?
- Does the same URL work from another client?
- Is there a browser proxy or VPN that changes the connection path?

Try accessing the site again after confirming the URL.

## Step 2: Check the Laptop's Network

On a Linux laptop:

```bash
ip -br addr
ip route
```

On Windows PowerShell or Command Prompt:

```powershell
ipconfig /all
route print
```

Check:
- Does the laptop have an IP address?
- Is the network interface connected?
- Is there a default gateway?
- Is the laptop on the expected network or VPN?

For Linux, inspect the default route:

```bash
ip route show default
```

For Windows, inspect the default gateway in `ipconfig /all`.

**If the laptop has no valid IP address or default route, fix the local network before troubleshooting the web server.**

## Step 3: Check DNS Name Resolution

Check whether the hostname resolves to an IP address.

Linux:

```bash
getent hosts vp-cloud
nslookup vp-cloud
```

If the hostname is a fully qualified domain name, test that exact name too:

```bash
nslookup vp-cloud.example.com
```

If available:

```bash
dig vp-cloud
dig +short vp-cloud
```

Windows:

```powershell
nslookup vp-cloud
```

### Interpret the result

- **Name resolves to an IP:** Continue to routing and connectivity tests.
- **Name does not resolve:** Investigate DNS configuration, search domains, and DNS records.
- **Wrong IP returned:** Check the DNS record or local hostname mapping.
- **Short name fails but fully qualified name works:** The search domain or hostname configuration may be missing.

On Linux, inspect resolver configuration:

```bash
cat /etc/resolv.conf
grep '^hosts:' /etc/nsswitch.conf
cat /etc/hosts
```

If `vp-cloud` is a private lab hostname, it may only resolve on the intended DNS server or VPN. Do not assume that a public DNS server will know about it.

**Do not edit DNS configuration until you know which resolver and hostname the environment expects.**

## Step 4: Test Basic Network Reachability

After obtaining the server IP address, test the network path.

```bash
ping -c 4 <SERVER_IP>
```

Example:

```bash
ping -c 4 192.168.1.50
```

Check the route:

```bash
ip route get <SERVER_IP>
```

On Windows:

```powershell
ping <SERVER_IP>
tracert <SERVER_IP>
```

On Linux, if installed:

```bash
traceroute <SERVER_IP>
```

### Interpret the result

- **Ping succeeds:** The host responds to ICMP, but this does not prove the website is working.
- **Ping fails:** ICMP may be blocked, the host may be down, or a route may be missing. Continue with TCP tests.
- **Route is missing or incorrect:** Investigate the laptop's routing, VPN, gateway, and network configuration.
- **Different networks cannot communicate:** Check routing between networks and firewall rules.

A failed ping alone is not proof that the server is down.

## Step 5: Test HTTP and HTTPS Ports

A website commonly uses:

- TCP port `80` for HTTP.
- TCP port `443` for HTTPS.

From Linux:

```bash
curl -v --connect-timeout 5 http://vp-cloud/
curl -vk --connect-timeout 5 https://vp-cloud/
```

The `-k` option skips certificate verification and is for diagnosis only. Do not use it as the permanent solution to a certificate problem.

If you know the server IP but DNS is failing, test the IP directly:

```bash
curl -v --connect-timeout 5 http://<SERVER_IP>/
```

If the website uses a hostname-based virtual host, test with the hostname preserved:

```bash
curl -v --resolve vp-cloud:80:<SERVER_IP> http://vp-cloud/
```

For HTTPS:

```bash
curl -vk --resolve vp-cloud:443:<SERVER_IP> https://vp-cloud/
```

On Windows PowerShell, `curl.exe` can be used to avoid PowerShell's `curl` alias.

### Check whether the TCP port is reachable

If `nc` is installed:

```bash
nc -vz -w 5 <SERVER_IP> 80
nc -vz -w 5 <SERVER_IP> 443
```

### Interpret the result

- **Connection timed out:** Possible packet filtering, routing failure, server outage, or dropped traffic.
- **Connection refused:** The host is reachable, but the port may not have a listener or may be actively rejected.
- **HTTP response received:** The web endpoint is responding; investigate the HTTP status or application if the site is still unusable.
- **TLS/certificate error:** Check HTTPS configuration and certificate validity.

## Step 6: Check Whether the Server Is Running

Connect to the server using SSH or the approved console, if available.

```bash
ssh bob@<SERVER_IP>
```

If SSH fails, do not immediately conclude that the server is down. SSH could be blocked or its service could be stopped.

Use the cloud provider's console, hypervisor console, or authorized out-of-band management if necessary.

Once logged into the server, run:

```bash
uptime
hostname
ip -br addr
ip route
```

Check:
- Is this the correct server?
- Does it have the expected IP address?
- Is its network interface up?
- Is the server responsive?
- Has it recently rebooted?

If the server is powered off or unreachable through all approved management paths, investigate the VM or physical host state.

**Only restart or power on the server when you have evidence it is stopped and authorization to do so.**

## Step 7: Check Whether the Web Server Service Is Running

Identify which web server the system uses.

For Nginx:

```bash
sudo systemctl status nginx
```

For Apache, the service name varies:

```bash
sudo systemctl status httpd
```

On Debian/Ubuntu, Apache commonly uses:

```bash
sudo systemctl status apache2
```

Check whether the service is enabled:

```bash
sudo systemctl is-enabled nginx
```

Replace `nginx` with the actual service name.

### If the service is stopped

For Nginx:

```bash
sudo nginx -t
sudo systemctl start nginx
sudo systemctl status nginx
```

For Apache on CentOS/RHEL:

```bash
sudo apachectl configtest
sudo systemctl start httpd
sudo systemctl status httpd
```

For Apache on Debian/Ubuntu:

```bash
sudo apachectl configtest
sudo systemctl start apache2
sudo systemctl status apache2
```

**Important:** Validate the configuration before starting or restarting a service. If the configuration test fails, correct the reported issue first.

## Step 8: Check Whether the Web Server Is Listening on Ports 80 and 443

Run on the server:

```bash
sudo ss -lntp
```

Filter for web ports:

```bash
sudo ss -lntp | grep -E ':(80|443)\b'
```

Expected examples may include listeners such as:

```text
LISTEN ... 0.0.0.0:80 ...
LISTEN ... 0.0.0.0:443 ...
```

Interpretation:
- `0.0.0.0:80` — listening on all IPv4 interfaces.
- `127.0.0.1:80` — listening only on loopback; remote clients cannot connect directly to that listener.
- `[::]:443` — listening on IPv6 addresses, subject to system configuration.
- No listener — the service may be stopped, configured for another port, or failing to start.

A service may intentionally listen only on a private interface, so compare the listener with the expected network architecture.

## Step 9: Check the Server Firewall and Network Security Rules

On the server, inspect the firewall.

For firewalld systems:

```bash
sudo firewall-cmd --state
sudo firewall-cmd --list-all
```

For systems using UFW:

```bash
sudo ufw status verbose
```

If `nftables` is used:

```bash
sudo nft list ruleset
```

Check the infrastructure firewall too:
- Cloud security groups
- Network ACLs
- Router or VPN access-control rules
- Corporate firewall policies

Ensure that the intended client network is allowed to reach the server on the required TCP port.

Do not disable the firewall as a troubleshooting shortcut. Make only the necessary, authorized rule changes.

## Step 10: Test the Website Locally on the Server

Run:

```bash
curl -v http://127.0.0.1/
curl -v http://localhost/
```

For HTTPS, if configured:

```bash
curl -vk https://127.0.0.1/
```

If the web server uses name-based virtual hosts, test the expected hostname:

```bash
curl -v -H 'Host: vp-cloud' http://127.0.0.1/
```

Interpretation:
- **Local request succeeds, remote request fails:** Investigate binding address, firewall, routing, or external security rules.
- **Local request fails:** Investigate service status, listener, configuration, or application.
- **Default page appears instead of the expected site:** Check the virtual-host/server-block configuration and document root.

## Step 11: Check Web Server Configuration

For Nginx:

```bash
sudo nginx -t
sudo nginx -T
```

For Apache:

```bash
sudo apachectl configtest
```

Check:
- Correct hostname/server name
- Correct listening port
- Correct document root
- Correct virtual-host or server-block configuration
- Correct upstream application address, if using a reverse proxy
- Recent configuration changes

Review the output carefully because full configuration dumps may contain sensitive values.

## Step 12: Check Web Server Logs

### Nginx

```bash
sudo journalctl -u nginx --since "30 minutes ago"
sudo tail -n 100 /var/log/nginx/error.log
sudo tail -n 100 /var/log/nginx/access.log
```

### Apache on CentOS/RHEL

```bash
sudo journalctl -u httpd --since "30 minutes ago"
sudo tail -n 100 /var/log/httpd/error_log
sudo tail -n 100 /var/log/httpd/access_log
```

### Apache on Debian/Ubuntu

```bash
sudo journalctl -u apache2 --since "30 minutes ago"
sudo tail -n 100 /var/log/apache2/error.log
sudo tail -n 100 /var/log/apache2/access.log
```

Look for:
- Permission denied
- Missing files
- Configuration syntax errors
- Address already in use
- Failed upstream connections
- TLS certificate errors
- Repeated 5xx responses

Log paths may vary by distribution and configuration.

## Step 13: Check Application Health and Website Content

If the web server is a reverse proxy, the application behind it may be down.

Check:
- Application service status
- Application logs
- Upstream port availability
- Database or dependency health
- File permissions and document root
- Disk space and memory

Useful commands:

```bash
df -h
free -h
uptime
sudo journalctl -p err --since "30 minutes ago"
```

For a known application service:

```bash
sudo systemctl status <application-service>
sudo journalctl -u <application-service> --since "30 minutes ago"
```

Use the actual service name instead of the placeholder.

## Step 14: Restore the Service Safely

Choose the fix that matches the evidence.

Examples:
- DNS record wrong → correct the appropriate DNS record.
- Route missing → correct the route or network configuration.
- Firewall blocks web traffic → add the required authorized rule.
- Web server stopped → validate configuration and start the service.
- Configuration invalid → correct the error, validate, then reload or restart.
- Application stopped → diagnose dependencies and restore the application.
- Disk full → investigate usage and safely free required space.
- TLS certificate invalid → renew or correct the certificate configuration.

After changing Nginx configuration:

```bash
sudo nginx -t && sudo systemctl reload nginx
```

For Apache, validate the configuration first, then reload the correct service:

```bash
sudo apachectl configtest
sudo systemctl reload httpd
```

On Debian/Ubuntu, use `apache2` instead of `httpd`.

If a reload is not supported or insufficient, use a restart only when appropriate and after considering service impact.

## Step 15: Verify Recovery from the Laptop

Test the exact URL from the original laptop and network.

```bash
getent hosts vp-cloud
curl -v --connect-timeout 5 http://vp-cloud/
```

For HTTPS:

```bash
curl -v --connect-timeout 5 https://vp-cloud/
```

Also verify:
- The expected page loads in the browser.
- HTTP redirects to HTTPS if that is intended.
- The certificate is valid.
- The response code is appropriate.
- A second client or network can access the site, if applicable.
- Logs no longer show the original failure.

Do not declare the incident resolved based only on the service showing `active`. Confirm the end-to-end user experience.

---

## Common Error Interpretation

| Symptom | Possible causes | First checks |
|---|---|---|
| `Could not resolve host` | DNS or hostname resolution failure | `getent hosts`, `dig`, `/etc/hosts` |
| Connection timed out | Routing, firewall, server outage, dropped traffic | `ip route`, `traceroute`, TCP port test |
| Connection refused | No listener or active rejection | `ss -lntp`, service status |
| HTTP 403 | Access denied or permissions/policy | Web server configuration and logs |
| HTTP 404 | Resource or route not found | URL, document root, application routes |
| HTTP 502 | Reverse proxy cannot get a valid upstream response | Upstream service and proxy logs |
| HTTP 503 | Service unavailable or overloaded | Application health, capacity, logs |
| TLS certificate error | Certificate, hostname, or trust problem | Certificate configuration and expiry |
| Website works locally only | Binding, firewall, routing, or security rules | Listener address, firewall, external route |
| Website works by IP but not name | DNS, search domain, virtual-host mismatch | DNS lookup and hostname configuration |

## Incident Closure Checklist

- [ ] Confirmed the correct hostname and URL.
- [ ] Checked laptop IP configuration and default route.
- [ ] Checked DNS resolution and returned IP.
- [ ] Tested network reachability and HTTP/HTTPS ports.
- [ ] Verified server power/state and management access.
- [ ] Checked web server service status.
- [ ] Checked listeners on the required ports.
- [ ] Checked host firewall and network security rules.
- [ ] Tested the website locally on the server.
- [ ] Checked web server configuration and logs.
- [ ] Checked application and dependencies.
- [ ] Applied the smallest appropriate fix.
- [ ] Verified the website from the original laptop.
- [ ] Recorded root cause, corrective action, and prevention steps.

## Key DevOps Principle

Troubleshoot from the client to the application in layers. Do not restart the server or disable the firewall as the first action. Collect evidence, identify the failing layer, make a controlled change, and verify the entire request path afterward.
