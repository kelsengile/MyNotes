[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# netstat (network statistics)

`netstat` displays network connections, listening ports, routing tables, and interface statistics. For decades it was the default first tool reached for when diagnosing "what's using this port?" or "what is my machine connected to right now?" — though on modern Linux it's officially deprecated in favor of `ss`.

Reference: [https://man7.org/linux/man-pages/man8/netstat.8.html](https://man7.org/linux/man-pages/man8/netstat.8.html)

---

## What Is netstat?

`netstat` reads networking state directly from the kernel (historically via `/proc/net/*` on Linux) and formats it into human-readable tables: which sockets exist, what state each TCP connection is in, which process owns a socket, and how much traffic has passed through each interface. It's available across Linux, macOS, BSD, and Windows, though the exact flags differ meaningfully by platform.

On modern Linux distributions, `netstat` comes from the `net-tools` package, which the kernel and distro maintainers have marked as legacy in favor of `iproute2`'s `ss` and `ip` — but `netstat` remains extremely widely known and is still installed (or easily installable) almost everywhere, including Windows, where it's a built-in Command Prompt/PowerShell tool with no `ss` equivalent.

---

## Core Commands (Linux/macOS)

```bash
netstat -tuln           # TCP & UDP listening sockets, numeric ports, no DNS lookups
netstat -tulnp          # Same, plus the PID/process name owning each socket (needs root for other users' processes)
netstat -a               # All sockets, listening and established
netstat -r               # Routing table
netstat -i               # Interface statistics (packets, errors)
netstat -s               # Per-protocol summary statistics (TCP, UDP, ICMP counters)
netstat -c               # Continuously refresh, like a live view
```

## Core Commands (Windows)

```
netstat -a               # All connections and listening ports
netstat -b               # Show the executable involved in each connection (needs admin)
netstat -n               # Numeric addresses/ports, skip name resolution
netstat -o               # Show the owning process ID
netstat -an              # Common combo: all connections, numeric form
```

## Flag Reference

| Flag | Meaning |
|---|---|
| `-t` | TCP sockets |
| `-u` | UDP sockets |
| `-l` | Listening sockets only |
| `-n` | Numeric addresses/ports (skip reverse DNS, much faster) |
| `-p` | Show the PID/program name (Linux; needs privileges for other users' sockets) |
| `-a` | All sockets (listening + established) |
| `-r` | Display the routing table |
| `-i` | Interface statistics |
| `-s` | Protocol-level statistics summary |
| `-o` (Windows) | Show owning process ID |
| `-b` (Windows) | Show the executable name for each connection |

---

## Reading TCP Connection States

The "State" column reflects the TCP state machine:

| State | Meaning |
|---|---|
| `LISTEN` | Waiting for incoming connections |
| `ESTABLISHED` | Active, fully connected session |
| `TIME_WAIT` | Recently closed, waiting to ensure delayed packets are discarded |
| `CLOSE_WAIT` | Remote end closed; local application hasn't closed its side yet |
| `SYN_SENT` | This side initiated a connection, awaiting response |
| `SYN_RECV` | Received a connection request, handshake in progress |

A large number of `TIME_WAIT` connections is usually normal on a busy server; a large, growing number of `CLOSE_WAIT` connections often indicates an application bug (not closing sockets properly), which is exactly the kind of thing `netstat` is used to catch.

---

## What netstat Is Used to Diagnose

- **"Something is already using port 8080."** — `netstat -tulnp | grep 8080` finds the offending process.
- **Detecting unexpected open ports** — an important basic security check: reviewing `netstat -tuln` for services listening that shouldn't be.
- **Diagnosing connection leaks** — watching `ESTABLISHED` or `CLOSE_WAIT` counts climb over time can reveal an application not releasing connections properly.
- **Confirming a service is actually listening** after starting it, before assuming a firewall or config issue.
- **Reviewing routing** via `-r`, as an alternative to `ip route` or `route print`.

---

## netstat vs. ss

`ss` ("socket statistics") reads the same kernel data more efficiently (via Netlink rather than parsing `/proc/net/*` line by line) and is noticeably faster on systems with very large numbers of connections. Flag conventions are similar by design:

```bash
ss -tuln      # roughly equivalent to netstat -tuln
ss -tulnp     # roughly equivalent to netstat -tulnp
```

Most Linux guidance today recommends learning `ss` for day-to-day Linux work, while still recognizing `netstat`'s syntax since it remains the standard on Windows and in a huge amount of existing documentation, tutorials, and older scripts.

---

## Related Tools

- `ss` — the modern Linux replacement, faster and still actively maintained.
- `lsof -i` — lists open files, filterable to network sockets specifically, with rich per-process detail.
- `nmap` — port-scans other hosts (rather than inspecting the local machine's own sockets).
- `ip` — routing table and interface configuration on modern Linux.

---

## Example Walkthrough

```bash
netstat -tulnp | grep :443
netstat -an | grep ESTABLISHED | wc -l
```

Confirms which process is listening on port 443 (HTTPS), then counts how many established connections currently exist on the machine — two very common first moves when investigating "is my web server actually running and receiving traffic?"

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)