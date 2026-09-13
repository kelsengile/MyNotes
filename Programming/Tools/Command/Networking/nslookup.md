[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# nslookup

`nslookup` queries DNS servers to resolve domain names to IP addresses (and vice versa). It predates `dig` and is available by default on both Windows and Unix-like systems, making it a common cross-platform fallback for basic DNS checks.

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/nslookup](https://learn.microsoft.com/windows-server/administration/windows-commands/nslookup)

---

## What Is nslookup?

`nslookup` can be used in two modes: **non-interactive**, giving a single query and getting one result back immediately, or **interactive**, where it opens a prompt letting you issue several queries, change the record type, or switch DNS servers without retyping the domain each time.

---

## Core Commands

```bash
nslookup example.com                # Look up the A record (IP address) for a domain
nslookup example.com 8.8.8.8         # Query a specific DNS server
nslookup -type=MX example.com        # Query mail server records
nslookup -type=NS example.com        # Query nameserver records
nslookup -type=TXT example.com       # Query TXT records
```

---

## Interactive Mode

```
nslookup
> server 8.8.8.8
> set type=MX
> example.com
> exit
```

Interactive mode is useful for running several related lookups without repeating the domain, and `set type=` changes which DNS record type subsequent queries return — mirroring what a single `dig` invocation with a type argument does, just spread across a session rather than one command.

---

## Common Record Types

| Type | Meaning |
|---|---|
| `A` | IPv4 address |
| `AAAA` | IPv6 address |
| `MX` | Mail exchanger (mail server) records |
| `NS` | Authoritative nameservers |
| `TXT` | Arbitrary text records, often for verification |
| `CNAME` | Alias for another domain |
| `SOA` | Zone administrative information |

---

## Reverse Lookups

```bash
nslookup 93.184.216.34
```

Given an IP address instead of a domain name, `nslookup` automatically performs a reverse DNS lookup (a `PTR` query), returning the hostname associated with that IP, if one is configured.

---

## Querying a Specific DNS Server

```bash
nslookup example.com 1.1.1.1
```

Adding a server address after the domain queries that specific resolver directly instead of your system's configured default — the same idea as `dig @server`, useful for comparing results across resolvers or bypassing a misbehaving local DNS cache.

---

## nslookup vs dig

`nslookup` is simpler and available everywhere by default, which makes it convenient for a quick check, but its output is less detailed (it doesn't show TTLs or full record metadata as clearly) and its behavior has historically varied slightly between implementations. `dig`, where available, is generally preferred for serious DNS troubleshooting because of its more consistent, detailed, and script-friendly output — but `nslookup` remains the more universally available option, especially on Windows.

---

## Common Gotchas

- Deprecated status: `nslookup`'s own documentation notes it's considered somewhat legacy in favor of `dig` and `host` on Unix-like systems, though it remains widely used and fully functional.
- Ambiguous non-authoritative answers: `nslookup` often labels answers "Non-authoritative," meaning the answer came from a resolver's cache rather than directly from the domain's authoritative nameserver — usually fine, but worth knowing when chasing a very recent DNS change.

---

## Example Walkthrough

```bash
nslookup example.com
nslookup -type=MX example.com
```

Checks a domain's basic IP resolution, then checks its mail server configuration — a common quick pair of checks when troubleshooting either a site or an email delivery issue.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Networking/ip.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# ip

The `ip` command is the modern Linux tool for viewing and configuring network interfaces, IP addresses, routing tables, and more — replacing the older `ifconfig`/`route`/`arp` tools with one unified command.

Download: [https://man7.org/linux/man-pages/man8/ip.8.html](https://man7.org/linux/man-pages/man8/ip.8.html)

---

## What Is ip?

`ip` is part of the `iproute2` package and organizes its functionality into **objects** (like `address`, `link`, `route`, `neigh`) each with their own set of actions (`show`, `add`, `del`, `set`). This object-based structure is more consistent and extensible than the older single-purpose tools it replaced, and supports newer networking features (like network namespaces) that those older tools never had.

---

## Core Commands

```bash
ip addr show                  # Show all IP addresses assigned to network interfaces
ip a                           # Shorthand for the same thing
ip link show                   # Show network interfaces and their status
ip route show                  # Show the routing table
ip addr add 192.168.1.100/24 dev eth0   # Assign an IP address to an interface
ip link set eth0 up            # Bring an interface up
ip link set eth0 down          # Bring an interface down
```

---

## Object/Action Structure

| Object | Common Actions | Purpose |
|---|---|---|
| `addr` | `show`, `add`, `del` | IP addresses on interfaces |
| `link` | `show`, `set` | Network interface state (up/down, MTU) |
| `route` | `show`, `add`, `del` | Routing table entries |
| `neigh` | `show` | ARP/neighbor table (like the old `arp` command) |
| `netns` | `add`, `list`, `exec` | Network namespaces |

Most `ip` subcommands can be abbreviated as long as they're unambiguous — `ip a` for `ip addr show`, `ip r` for `ip route show`, and so on.

---

## Viewing Interfaces and Addresses

```bash
ip addr show eth0
ip -4 addr show          # IPv4 addresses only
ip -6 addr show          # IPv6 addresses only
```

The output for each interface shows its state (`UP`/`DOWN`), assigned IP addresses with their subnet mask (in CIDR notation, e.g. `/24`), and MAC address — everything `ifconfig` used to show, in a more structured format.

---

## Managing Routes

```bash
ip route show
ip route add 10.0.0.0/24 via 192.168.1.1
ip route del 10.0.0.0/24
ip route get 8.8.8.8       # Show which route would be used to reach a specific address
```

`ip route get` is particularly useful for debugging: it doesn't send any traffic, it just reports which interface and gateway the kernel would choose to reach a given destination, which quickly reveals routing misconfigurations.

---

## Bringing Interfaces Up and Down

```bash
sudo ip link set eth0 down
sudo ip link set eth0 up
sudo ip link set eth0 mtu 1400
```

Toggling an interface down and back up is a common troubleshooting step (similar in spirit to restarting a device), and setting MTU is occasionally necessary to work around network paths that don't handle the default packet size well (e.g. certain VPN configurations).

---

## Neighbor Table (ARP)

```bash
ip neigh show
```

Shows the mapping between IP addresses and MAC addresses that the kernel currently has cached for the local network — the direct replacement for the older, separate `arp -a` command.

---

## Common Gotchas

- Changes don't persist by default: commands like `ip addr add` take effect immediately but are lost on reboot unless also configured in your distribution's persistent network configuration (e.g. Netplan, NetworkManager, or `/etc/network/interfaces`).
- Old habits: many tutorials still reference `ifconfig` and `route`, which are deprecated on modern Linux distributions in favor of `ip` — worth translating old commands to their `ip` equivalents when following older guides.

---

## Example Walkthrough

```bash
ip addr show
ip route get 8.8.8.8
```

Checks the machine's current IP addresses, then confirms which interface and gateway would actually be used to reach the public internet — a fast way to sanity-check basic network configuration.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)