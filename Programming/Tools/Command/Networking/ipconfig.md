[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# ipconfig

`ipconfig` displays and manages network configuration on Windows — IP addresses, subnet masks, gateways, and DNS settings for each network adapter.

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/ipconfig](https://learn.microsoft.com/windows-server/administration/windows-commands/ipconfig)

---

## What Is ipconfig?

`ipconfig` is the Windows equivalent of Linux's `ip addr` / `ifconfig`, showing per-adapter network configuration. Beyond just displaying settings, it also exposes controls over the DHCP lease (the process by which a machine automatically obtains an IP address from a router) and the local DNS resolver cache, which the Linux `ip` command doesn't handle in the same way.

---

## Core Commands

```
ipconfig                    # Show basic IP configuration for all adapters
ipconfig /all               # Show detailed configuration, including MAC address and DNS servers
ipconfig /release            # Release the current DHCP-assigned IP address
ipconfig /renew              # Request a new IP address from the DHCP server
ipconfig /flushdns           # Clear the local DNS resolver cache
ipconfig /displaydns         # Show the current contents of the DNS cache
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `/all` | Full configuration detail for every adapter |
| `/release` | Release the DHCP lease (adapter loses its IP) |
| `/renew` | Request a fresh IP address from DHCP |
| `/flushdns` | Clear cached DNS lookups |
| `/displaydns` | Show cached DNS entries |
| `/registerdns` | Manually re-register the machine's DNS name with the DNS server |

---

## Reading the Output

```
Ethernet adapter Ethernet:
   IPv4 Address. . . . . . . . . . . : 192.168.1.42
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.1.1
```

The **IPv4 Address** is the machine's current address on the network, the **Subnet Mask** defines the size of the local network, and the **Default Gateway** is the router traffic is sent to when the destination is outside the local subnet — the three pieces of information needed to diagnose most basic connectivity issues.

---

## Fixing Common Network Issues

```
ipconfig /release
ipconfig /renew
ipconfig /flushdns
```

This three-command sequence is one of the most common Windows network troubleshooting rituals: releasing and renewing the DHCP lease forces the machine to request fresh network settings from the router (fixing many "stuck" IP configuration issues), and flushing DNS clears out any stale cached lookups that might be pointing to an outdated or incorrect address.

---

## Viewing Full Adapter Details

```
ipconfig /all
```

The default `ipconfig` output is deliberately terse; `/all` reveals the adapter's MAC address, DHCP server, DNS servers, and lease expiration time — everything needed for a full network diagnostic report.

---

## Common Gotchas

- Requires elevated prompt for some actions: `/release` and `/renew` may require running Command Prompt as Administrator, depending on system configuration and adapter type.
- Multiple adapters: a machine with Wi-Fi, Ethernet, and virtual adapters (like VPN or VM network adapters) will list all of them — it's easy to look at the wrong adapter's settings if not checking adapter names carefully.

---

## Example Walkthrough

```
ipconfig /all
ipconfig /flushdns
```

Views full network configuration details for every adapter, then clears the DNS cache — a typical pair of steps when troubleshooting a machine that can't reach a particular website even though the network otherwise works.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Networking/netstat.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# netstat (network statistics)

`netstat` displays active network connections, listening ports, and routing/interface statistics — a core tool for seeing what's actually talking on the network from a given machine.

Download: [https://man7.org/linux/man-pages/man8/netstat.8.html](https://man7.org/linux/man-pages/man8/netstat.8.html)

---

## What Is netstat?

`netstat` reports the state of the machine's network stack: which TCP/UDP ports are open and listening, which remote connections are currently established, and which process owns each connection (with the right permissions). This makes it a primary tool for answering questions like "what's listening on port 8080?" or "is this suspicious outbound connection coming from?"

---

## Core Commands

```bash
netstat -a                  # Show all active connections and listening ports
netstat -tulpn               # TCP/UDP listening ports with process info (Linux)
netstat -an                  # All connections, numeric addresses (no DNS lookups)
netstat -r                   # Show the routing table
netstat -s                   # Show per-protocol summary statistics
netstat -i                   # Show network interface statistics
```

---

## Common Flags

| Flag | Meaning |
|---|---|
| `-a` | Show all connections and listening ports |
| `-t` | TCP connections only |
| `-u` | UDP connections only |
| `-l` | Listening sockets only |
| `-n` | Numeric addresses/ports, skip DNS/service-name resolution (faster) |
| `-p` | Show the PID/program name owning each connection (Linux, needs elevated privileges for other users' processes) |
| `-r` | Display the routing table |
| `-c` | Continuously refresh output |

---

## Finding What's Using a Port

```bash
netstat -tulpn | grep :8080
```

This is the classic "what's listening on port 8080" one-liner: `-t`/`-u` for TCP/UDP, `-l` for listening sockets, `-p` to show the owning process, `-n` for fast numeric output, filtered with `grep` down to the port of interest.

---

## Reading Connection States

```
Proto  Local Address       Foreign Address     State
tcp    0.0.0.0:22          0.0.0.0:*           LISTEN
tcp    192.168.1.5:54321   93.184.216.34:443   ESTABLISHED
```

`LISTEN` means a service is waiting for incoming connections on that port (like SSH on port 22 here). `ESTABLISHED` means an active, two-way connection is currently in progress. Other states like `TIME_WAIT` and `CLOSE_WAIT` reflect a connection winding down — a large number of connections stuck in `CLOSE_WAIT` can indicate an application that isn't properly closing sockets.

---

## netstat's Deprecation on Linux

On many modern Linux distributions, `netstat` is deprecated in favor of the newer `ss` command from the same `iproute2` package as the `ip` command, since `ss` is faster and provides more detailed socket information. `netstat` remains widely used, well documented, and available cross-platform (including Windows and macOS), which is why it's still worth knowing even where `ss` is the more "modern" recommendation.

---

## Windows Usage

```
netstat -ano
```

On Windows, `-a` shows all connections, `-n` shows numeric addresses, and `-o` shows the owning process ID (which can then be cross-referenced in Task Manager or with `tasklist` to find the actual program).

---

## Common Gotchas

- Permission limits: viewing which process owns a connection (`-p` on Linux) for processes owned by other users typically requires root/administrator privileges.
- DNS resolution slowness: without `-n`, `netstat` tries to reverse-resolve every IP address to a hostname, which can make output appear to hang on a machine with many connections or a slow DNS server.

---

## Example Walkthrough

```bash
netstat -tulpn | grep LISTEN
```

Lists every port currently listening for connections along with the process behind it — a quick way to audit what services are exposed on a machine.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)