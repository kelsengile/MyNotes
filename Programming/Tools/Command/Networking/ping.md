[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# ping

`ping` is a network diagnostic command used to test whether a remote host is reachable, and to measure how long a round trip to it takes. It's usually the very first tool people reach for when troubleshooting "is the internet down?"

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/ping](https://learn.microsoft.com/windows-server/administration/windows-commands/ping)

---

## What Is ping?

`ping` sends small ICMP "echo request" packets to a target and waits for "echo reply" packets back. Getting replies means the host is reachable and responding; no replies (or timeouts) point to a network, routing, or firewall problem somewhere between you and the destination. Because ICMP is a separate, lightweight protocol from the TCP/UDP traffic that actual applications use, a successful `ping` confirms basic reachability but doesn't guarantee a specific service (like a website's port 443) is actually working — some firewalls block ICMP entirely while still allowing normal web traffic.

---

## Core Commands

```bash
ping example.com              # Send continuous pings (Linux/macOS) until you Ctrl+C
ping -c 4 example.com         # Send exactly 4 pings, then stop (Linux/macOS)
ping -n 4 example.com         # Send exactly 4 pings, then stop (Windows)
ping -t example.com           # Ping continuously until stopped (Windows)
ping -i 2 example.com         # Wait 2 seconds between pings (Linux/macOS)
```

Note the platform difference: Linux/macOS `ping` runs forever by default and needs `-c` to limit the count, while Windows `ping` stops after 4 by default and needs `-t` to run continuously.

---

## All Major Options (Linux/macOS)

| Flag | Meaning |
|---|---|
| `-c N` | Send exactly N packets, then stop |
| `-i sec` | Interval between packets, in seconds |
| `-s size` | Set the packet payload size in bytes |
| `-W sec` | Timeout, in seconds, to wait for each reply |
| `-4` / `-6` | Force IPv4 or IPv6 |
| `-q` | Quiet — only show summary statistics at the end |
| `-f` | Flood ping (send as fast as possible — requires root; can overwhelm a network) |

---

## Reading the Output

```
64 bytes from example.com: icmp_seq=1 ttl=56 time=13.2 ms
```

`time` is the round-trip latency in milliseconds. `ttl` (Time To Live) roughly indicates how many network hops the packet took to arrive — a much lower TTL than expected can hint at an unusual routing path. `icmp_seq` numbers packets sequentially, so gaps in the sequence (packet 3 missing between 2 and 4) indicate dropped packets, a sign of network instability.

---

## Summary Statistics

```
--- example.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 12.877/13.245/13.601/0.263 ms
```

The summary at the end reports packet loss percentage (any loss above 0% suggests network problems) and round-trip time statistics — `mdev` (mean deviation/jitter) is especially relevant for real-time applications like voice or video calls, where inconsistent latency causes more noticeable problems than moderately high but stable latency.

---

## Testing With Different Packet Sizes

```bash
ping -s 1472 example.com
```

Larger packets can reveal MTU (Maximum Transmission Unit) problems — if packets above a certain size consistently fail or fragment while small ones succeed, it often points to a misconfigured network device somewhere in the path that isn't handling fragmentation correctly.

---

## Common Gotchas

- Blocked ICMP: some servers and firewalls deliberately block ICMP echo requests for security reasons, so a failed `ping` doesn't always mean the host is down — the actual service (e.g. a website) might still work fine.
- Interpreting high latency: a single slow ping can be a fluke; looking at several packets' consistency (via the summary stats) gives a more reliable picture than one reading.

---

## Example Walkthrough

```bash
ping -c 4 8.8.8.8
ping -c 4 example.com
```

Pinging a known-reliable IP address (Google's public DNS) first checks whether your own internet connection works. Pinging the domain afterward checks whether DNS resolution and the specific site are the actual problem.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Networking/traceroute.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# traceroute

`traceroute` shows the sequence of routers (hops) that packets travel through on their way to a destination, along with the round-trip time to each hop — useful for identifying exactly where along a network path a problem is occurring.

Download: [https://man7.org/linux/man-pages/man8/traceroute.8.html](https://man7.org/linux/man-pages/man8/traceroute.8.html)

---

## What Is traceroute?

`traceroute` exploits the IP Time To Live (TTL) field: it sends a packet with TTL=1, which expires at the very first router and triggers that router to send back an ICMP "time exceeded" message revealing its address. It then sends TTL=2, learning the second hop, and so on, incrementing TTL until a packet finally reaches the destination. The result is a hop-by-hop map of the network path, which `ping` alone can't show since `ping` only reports on the final destination.

---

## Core Commands

```bash
traceroute example.com          # Trace the route to a host (Linux/macOS)
tracert example.com             # Same idea on Windows (see the tracert page for details)
traceroute -m 15 example.com    # Limit to a maximum of 15 hops
traceroute -q 1 example.com     # Send only 1 probe per hop instead of the default 3
traceroute -n example.com       # Skip reverse DNS lookups, showing raw IPs (much faster)
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-m hops` | Maximum number of hops to probe before giving up |
| `-q N` | Number of probe packets sent per hop (default 3) |
| `-n` | Don't resolve IP addresses to hostnames (faster output) |
| `-w sec` | Timeout waiting for each probe's response |
| `-I` | Use ICMP echo requests instead of UDP probes |
| `-T` | Use TCP SYN probes, which are more likely to pass through firewalls |

---

## Reading the Output

```
 1  192.168.1.1 (192.168.1.1)  1.123 ms  1.045 ms  0.998 ms
 2  10.10.0.1 (10.10.0.1)      5.234 ms  5.198 ms  5.301 ms
 3  * * *
 4  93.184.216.34 (93.184.216.34)  15.6 ms  15.2 ms  15.9 ms
```

Each numbered line is one hop, with (by default) three round-trip times, one per probe. `* * *` means all three probes to that hop timed out — this doesn't necessarily mean the path is broken, since some routers deliberately deprioritize or block the ICMP/UDP responses `traceroute` relies on while still forwarding real traffic normally.

---

## Diagnosing Where a Problem Is

```bash
traceroute -n slow-site.com
```

If latency jumps sharply at one particular hop and stays high for every hop after it, that hop is likely where congestion or a problem is occurring. If a hop times out but subsequent hops respond normally, the intermediate router is probably just configured not to respond to traceroute probes rather than being genuinely broken.

---

## Choosing Probe Type for Firewalled Networks

```bash
traceroute -T -p 443 example.com
```

Default UDP-based traceroute is often blocked by firewalls partway through a path, causing every hop from that point on to show as `* * *`. Using `-T` (TCP probes, often on a commonly-open port like 443) can get further through firewalled networks since it looks more like ordinary web traffic.

---

## Common Gotchas

- Asymmetric routing: the path your packets take to a destination isn't guaranteed to be the same path replies take back, so a `traceroute` doesn't necessarily reflect the full round-trip route.
- Rate limiting: some routers intentionally slow down or drop ICMP "time exceeded" replies to reduce load, which can make specific hops look artificially slow or unresponsive without an actual problem existing there.

---

## Example Walkthrough

```bash
traceroute -n example.com
```

Running with `-n` to skip DNS lookups gives a fast, raw view of every router hop between you and a destination — a typical first step in escalating from "ping fails" to "where exactly does it fail."

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Networking/tracert.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tracert

`tracert` is the Windows equivalent of `traceroute`, showing the router hops between your machine and a destination host.

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/tracert](https://learn.microsoft.com/windows-server/administration/windows-commands/tracert)

---

## What Is tracert?

`tracert` works on the same TTL-expiry principle as Unix's `traceroute`: it sends packets with progressively increasing TTL values, and each router along the path that lets the TTL expire replies with an ICMP message revealing its address. The two key implementation differences from `traceroute` are that Windows `tracert` uses ICMP echo requests by default (rather than UDP), and its flag names follow Windows' single-letter, no-dash-required convention.

---

## Core Commands

```
tracert example.com            # Trace the route to a host
tracert -d example.com          # Don't resolve hostnames — show raw IP addresses only
tracert -h 15 example.com       # Limit to a maximum of 15 hops
tracert -w 2000 example.com     # Set a 2000ms (2 second) timeout per probe
tracert -4 example.com          # Force IPv4
tracert -6 example.com          # Force IPv6
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-d` | Don't resolve addresses to hostnames (faster) |
| `-h max_hops` | Maximum number of hops to search for the target |
| `-w timeout` | Wait time in milliseconds for each reply |
| `-4` / `-6` | Force IPv4 or IPv6 |
| `-j host-list` | Loose source route along a specified list of hosts (IPv4 only, rarely used today) |

---

## Reading the Output

```
  1    <1 ms    <1 ms    <1 ms  192.168.1.1
  2     5 ms     4 ms     5 ms  10.10.0.1
  3     *        *        *     Request timed out.
  4    15 ms    14 ms    16 ms  93.184.216.34
```

Each row is one hop, with three round-trip time samples. `Request timed out.` for a hop means that router didn't respond to the probe — often because it's configured not to, not necessarily because it's broken, especially if later hops respond normally.

---

## Speeding Up a Trace

```
tracert -d example.com
```

By default, `tracert` performs a reverse DNS lookup for every hop's IP address, which can add noticeable delay per hop, especially over slow or unresponsive DNS servers. `-d` skips this and shows raw IPs immediately, which is usually the faster option when you just need to see where latency is occurring rather than which organization owns each hop.

---

## Common Gotchas

- ICMP-based probing: because `tracert` uses ICMP by default (unlike Unix `traceroute`'s UDP default), firewalls that specifically block ICMP will cause a `tracert` from a Windows machine to fail even where a TCP-based trace might succeed.
- Corporate networks: internal hops on a corporate or ISP network are frequently configured to not respond to trace probes at all, producing several consecutive timeout lines that don't indicate an actual fault.

---

## Example Walkthrough

```
tracert -d example.com
```

Runs a fast trace (skipping hostname resolution) to see the full router path and per-hop latency to a destination — the standard first diagnostic step on Windows when a site is slow or unreachable.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)