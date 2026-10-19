# Linux Networking and DNS — Study Notes

## 1. Networking Fundamentals

A computer network allows systems to communicate and exchange data.

### Important networking components

| Component | Purpose | Example |
|---|---|---|
| Network interface | Connects a host to a network | `ens33`, `eth0` |
| IP address | Identifies a host on an IP network | `192.168.1.10` |
| Subnet | Defines a network and its address range | `192.168.1.0/24` |
| Switch | Connects devices within a local network, forwarding frames using MAC addresses | Ethernet switch |
| Router | Connects different IP networks | Home router |
| Default gateway | Next hop used for destinations outside local routes | `192.168.1.1` |
| DNS server | Resolves domain names into DNS records, such as IP addresses | `8.8.8.8` |
| Firewall | Controls permitted network traffic | `firewalld`, security groups |

### Switch vs Router

**Switch**
- Connects devices on the same local network.
- Primarily forwards Ethernet frames using MAC addresses.
- A Layer 2 switch does not normally route between IP subnets.

**Router**
- Forwards IP packets between networks.
- Uses a routing table to choose the next hop.
- A Linux host can also act as a router when configured to forward packets.

---

## 2. Basic Network Commands

### 2.1 `ip link` — Network interface status

List network interfaces:

```bash
ip link show
```

Bring an interface up or down:

```bash
sudo ip link set ens33 up
sudo ip link set ens33 down
```

Replace `ens33` with the actual interface name.

Use this when checking whether an interface is administratively enabled.

### 2.2 `ip addr` — IP addresses

Display interface addresses:

```bash
ip addr show
ip -br addr
ip -4 addr show
```

Add a temporary IP address:

```bash
sudo ip addr add 192.168.1.50/24 dev ens33
```

Remove it:

```bash
sudo ip addr del 192.168.1.50/24 dev ens33
```

These commands change the running configuration. Changes made this way generally do not survive a reboot.

**Persistent configuration:** Use NetworkManager connection profiles or the network configuration system used by your Linux distribution.

### 2.3 `ip route` — Routing and gateway

Display the routing table:

```bash
ip route
```

Show the default gateway:

```bash
ip route show default
```

Example:

```text
default via 192.168.1.1 dev ens33
192.168.1.0/24 dev ens33 proto kernel scope link src 192.168.1.50
```

In this example:
- `192.168.1.1` — default gateway.
- `ens33` — outgoing interface.
- `192.168.1.0/24` — directly connected network.
- `192.168.1.50` — local source IP address.

Add a temporary default route:

```bash
sudo ip route add default via 192.168.1.1
```

Add a route to a specific network:

```bash
sudo ip route add 10.10.0.0/16 via 192.168.1.1
```

Remove a route:

```bash
sudo ip route del 10.10.0.0/16
```

These routes are normally temporary and may disappear after reboot or network reconnection.

### 2.4 `ping` — Test connectivity

```bash
ping 192.168.1.1
ping example.com
```

Limit the number of packets:

```bash
ping -c 4 example.com
```

Use `ping` to test reachability and observe packet loss and latency.

**Important:** A failed ping does not always mean the server is down. A firewall may block ICMP, or the route may be unavailable.

### 2.5 Other useful commands

```bash
ip neigh
ss -tuln
traceroute example.com
curl -I https://example.com
```

- `ip neigh` — displays the local neighbor table, including discovered IP-to-MAC mappings.
- `ss -tuln` — lists listening TCP and UDP sockets.
- `traceroute` — investigates the network path to a destination, if installed.
- `curl -I` — checks HTTP response headers.

---

## 3. What Is DNS?

**DNS (Domain Name System)** translates domain names into records such as IP addresses.

For example:

```text
example.com → IP address
```

Without DNS, users and applications would often need to use IP addresses directly.

### DNS resolution flow

```text
Application
    ↓
Linux name-resolution libraries
    ↓
/etc/nsswitch.conf
    ↓
/etc/hosts or DNS resolver, depending on configuration
    ↓
Configured DNS server
    ↓
DNS answer returned to application
```

The exact path depends on the system's resolver configuration and enabled services.

---

## 4. DNS Name Hierarchy

DNS uses a hierarchical naming system.

Example:

```text
www.example.com.
```

The components are:

- `.` — root of the DNS hierarchy.
- `com` — top-level domain (TLD).
- `example` — second-level domain.
- `www` — subdomain or host label.

### DNS servers and their roles

**Root DNS servers**
- Direct queries toward the appropriate TLD name servers.

**TLD DNS servers**
- Direct queries toward the authoritative name servers for domains under a TLD.

**Authoritative DNS servers**
- Publish DNS records for a domain.
- Provide authoritative answers for records they manage.

**Recursive DNS resolvers**
- Resolve queries for clients.
- May cache answers to reduce repeated lookups.

Google Public DNS uses `8.8.8.8` as one of its IPv4 resolver addresses. It is not a root or authoritative server merely because it is a DNS server.

---

## 5. DNS Configuration Files

### 5.1 `/etc/resolv.conf`

This file contains resolver settings such as DNS server addresses and search domains.

Inspect it:

```bash
cat /etc/resolv.conf
```

Example:

```text
search mycompany.com
nameserver 192.168.1.1
nameserver 8.8.8.8
```

**`nameserver`**
- Specifies a DNS resolver IP address.
- Multiple entries may be configured.
- The resolver tries available servers according to system resolver behavior.

**`search`**
- Specifies domain suffixes that may be appended when resolving unqualified names.
- For example, a lookup for `server1` may also try `server1.mycompany.com`.

The search domain does not change DNS records. It influences how unqualified names are looked up.

**Important:** `/etc/resolv.conf` may be generated or managed by NetworkManager, `systemd-resolved`, DHCP, or another network service. Direct edits may be overwritten.

### 5.2 `/etc/hosts`

This file provides local hostname-to-address mappings.

Inspect it:

```bash
cat /etc/hosts
```

Example:

```text
127.0.0.1 localhost
192.168.1.20 appserver.mycompany.com appserver
```

If the configured name-resolution order checks `files` before DNS, an entry in `/etc/hosts` may be used without querying a DNS server.

### 5.3 `/etc/nsswitch.conf`

This file controls the order of name-service sources used by the system's name-service switch.

Inspect the host lookup rule:

```bash
grep '^hosts:' /etc/nsswitch.conf
```

Example:

```text
hosts: files dns
```

This means:
- `files` — check local sources such as `/etc/hosts`.
- `dns` — use DNS if needed.

Another system might use:

```text
hosts: files resolve dns
```

The exact order depends on the system.

**Remember:** `resolv.conf` configures resolver behavior; `nsswitch.conf` determines the sources and their order for name lookups.

---

## 6. DNS Lookup Commands

### 6.1 `nslookup`

Looks up DNS information for a hostname.

```bash
nslookup example.com
```

Query a particular DNS server:

```bash
nslookup example.com 8.8.8.8
```

Use it for quick DNS troubleshooting.

### 6.2 `dig`

`dig` provides detailed DNS query results and is useful for diagnostics.

```bash
dig example.com
```

Query only the IPv4 address records:

```bash
dig example.com A
```

Query an IPv6 address record:

```bash
dig example.com AAAA
```

Query a specific DNS server:

```bash
dig @8.8.8.8 example.com
```

Show a short answer:

```bash
dig +short example.com
```

Check the authoritative name servers:

```bash
dig example.com NS
```

Check mail exchange records:

```bash
dig example.com MX
```

### 6.3 `getent hosts`

Tests hostname resolution through the system's configured name-service mechanisms:

```bash
getent hosts example.com
getent hosts appserver
```

This can be especially useful when troubleshooting `/etc/hosts`, DNS, and NSS behavior together.

### `nslookup` vs `dig`

| Command | Main use |
|---|---|
| `nslookup` | Simple DNS queries |
| `dig` | Detailed DNS troubleshooting |
| `getent hosts` | Tests the system's configured hostname lookup path |
| `ping` | Tests network reachability; may also resolve a hostname |

---

## 7. Common DNS Record Types

| Record | Purpose | Example use |
|---|---|---|
| `A` | Maps a name to an IPv4 address | `app.example.com → 192.0.2.10` |
| `AAAA` | Maps a name to an IPv6 address | IPv6 service address |
| `CNAME` | Aliases one DNS name to another DNS name | `www.example.com → example.com` |
| `MX` | Identifies mail servers for a domain | Email delivery |
| `NS` | Identifies authoritative name servers | Delegating DNS authority |
| `PTR` | Reverse lookup from an IP address to a name | Reverse DNS |
| `TXT` | Stores text data | SPF and domain verification |
| `SOA` | Contains zone authority and timing information | DNS zone administration |
| `SRV` | Identifies services and their ports | Service discovery |

### Query record types

```bash
dig example.com A
dig example.com AAAA
dig example.com CNAME
dig example.com MX
dig example.com NS
dig example.com TXT
dig example.com SOA
```

Note that a DNS zone's root record and the exact response depend on the domain's actual configuration.

---

## 8. Configure a DNS Server Address

### Temporary example

On a lab system where the resolver file is not managed by another service, an administrator could edit `/etc/resolv.conf`:

```text
nameserver 8.8.8.8
```

But on many modern Linux systems, the file is generated automatically. A direct edit may not persist.

### Check NetworkManager connections

```bash
nmcli connection show
```

Inspect a connection's DNS settings:

```bash
nmcli connection show "Wired connection 1"
```

Replace the connection name with the one listed on your system.

For a NetworkManager connection, a persistent DNS configuration can be set with:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dns "8.8.8.8"
sudo nmcli connection modify "Wired connection 1" ipv4.ignore-auto-dns yes
```

Activate the change:

```bash
sudo nmcli connection up "Wired connection 1"
```

**Caution:** Use the correct connection name. Changing DNS can affect connectivity and name resolution. `8.8.8.8` is an example public resolver and may not be appropriate for private lab domains or corporate networks.

---

## 9. Troubleshooting: DNS vs Network Connectivity

When a hostname fails to resolve, troubleshoot in order.

### Step 1: Check the interface

```bash
ip -br addr
ip link show
```

Confirm the expected interface is up and has an IP address.

### Step 2: Check the routing table

```bash
ip route
ip route show default
```

Confirm a valid route exists to the destination or DNS server.

### Step 3: Test connectivity to the gateway

```bash
ping -c 4 192.168.1.1
```

Replace the example with the actual gateway address.

### Step 4: Check DNS configuration

```bash
cat /etc/resolv.conf
grep '^hosts:' /etc/nsswitch.conf
```

### Step 5: Query DNS directly

```bash
nslookup example.com
dig example.com
```

If available, query a specific resolver:

```bash
dig @8.8.8.8 example.com
```

This requires network access to that resolver, and outbound DNS may be blocked in some environments.

### Step 6: Test name resolution through the system

```bash
getent hosts example.com
```

### Interpret the results

- **IP connectivity works, but names fail:** DNS or name-resolution configuration may be the problem.
- **Direct DNS queries work, but `getent` fails:** Investigate `/etc/nsswitch.conf`, local host entries, or the system resolver service.
- **Neither IP connectivity nor DNS works:** Investigate interface status, routes, gateway, and firewall rules.
- **`ping` fails but DNS works:** ICMP might be blocked, or the destination may not respond to ping.

---

## 10. Quick Command Cheat Sheet

```bash
# Interfaces and IP addresses
ip link show
ip addr show
ip -br addr
ip -4 addr show

# Routing and gateway
ip route
ip route show default
ip neigh

# Connectivity
ping -c 4 example.com
curl -I https://example.com
ss -tuln

# DNS configuration
cat /etc/resolv.conf
cat /etc/hosts
grep '^hosts:' /etc/nsswitch.conf

# DNS lookup
nslookup example.com
nslookup example.com 8.8.8.8
dig example.com
dig +short example.com
dig @8.8.8.8 example.com A
getent hosts example.com

# Temporary network configuration
sudo ip addr add 192.168.1.50/24 dev ens33
sudo ip addr del 192.168.1.50/24 dev ens33
sudo ip link set ens33 up
sudo ip route add default via 192.168.1.1
```

## 11. DevOps Interview Revision

**1. What is DNS?**  
A distributed naming system that maps domain names to DNS records, including IP addresses.

**2. What is the difference between a recursive resolver and an authoritative server?**  
A recursive resolver finds answers for clients, often using caching. An authoritative server publishes DNS records for zones it manages.

**3. What is the purpose of `/etc/resolv.conf`?**  
It contains resolver settings such as nameserver addresses and search domains.

**4. What does `/etc/nsswitch.conf` control?**  
It specifies the order and sources used for system name resolution.

**5. What is the difference between `A` and `AAAA` records?**  
`A` maps a name to IPv4; `AAAA` maps a name to IPv6.

**6. What is a CNAME record?**  
An alias that points one DNS name to another DNS name.

**7. What is the difference between `ip link` and `ip addr`?**  
`ip link` manages interface link state and properties. `ip addr` displays or modifies IP addresses.

**8. How do you find the default gateway?**  
Use `ip route show default`.

**9. Why might an IP address added using `ip addr add` disappear after reboot?**  
That command modifies the running configuration. Persistent settings must be saved in the appropriate network connection configuration.

**10. How would you troubleshoot a server that responds to its IP but not its hostname?**  
Check resolver configuration, `/etc/hosts`, `/etc/nsswitch.conf`, and use `dig`, `nslookup`, and `getent hosts`.

## Learning Summary

- `ip link` — network interface status.
- `ip addr` — IP addresses.
- `ip route` — routing and gateway configuration.
- `/etc/resolv.conf` — resolver configuration.
- `/etc/hosts` — local hostname mappings.
- `/etc/nsswitch.conf` — name-service lookup order.
- `nslookup` and `dig` — DNS queries.
- `getent hosts` — system hostname resolution.
- `A`, `AAAA`, `CNAME`, `MX`, `NS`, and `PTR` — common DNS records.
- Persistent network configuration should be saved through the network management system used by the host.
