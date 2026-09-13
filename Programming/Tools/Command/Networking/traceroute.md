[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# traceroute

`traceroute` maps the path packets take to reach a destination host, showing every router (hop) along the way and how long each hop takes to respond. It's the standard tool for diagnosing *where* in a network path a slowdown or failure is happening, not just *whether* the destination is reachable.

Reference: [https://linux.die.net/man/8/traceroute](https://linux.die.net/man/8/traceroute)

---

## What Is traceroute?

`traceroute` exploits a feature of the IP protocol called TTL (Time To Live) — a counter each packet carries that's decremented by one at every router it passes through. When a router decrements the TTL to zero, it discards the packet and sends back an ICMP "Time Exceeded" message to the sender, identifying itself in the process.

`traceroute` sends a series of probe packets with TTL values starting at 1 and increasing by one each round. The first probe (TTL=1) dies at the very first router, which replies and reveals itself as "hop 1." The next probe (TTL=2) makes it one hop further before dying, revealing hop 2, and so on — until a probe finally reaches the actual destination, which replies directly instead of with a "Time Exceeded" message.

By default, on Unix-like systems `traceroute` sends UDP packets to a high, normally-unused port; on Windows, `tracert` uses ICMP Echo Requests instead. This difference matters because some firewalls treat UDP and ICMP traceroute probes very differently, causing the same route to look different depending on OS.

---

## Core Commands

```bash
traceroute example.com          # Trace the route to a host
traceroute -n example.com       # Numeric only, skip reverse DNS lookups (much faster)
traceroute -m 15 example.com    # Limit to 15 hops maximum
traceroute -q 1 example.com     # Send only 1 probe per hop instead of the default 3
traceroute -I example.com       # Use ICMP Echo instead of UDP (like tracert)
traceroute -T example.com       # Use TCP SYN probes (often gets past firewalls that block UDP/ICMP)
traceroute -p 443 -T example.com # TCP SYN probes to a specific port
```

## Flag Reference

| Flag | Meaning |
|---|---|
| `-n` | Skip reverse DNS lookups, show IPs only (much faster) |
| `-m N` | Maximum number of hops to probe (default 30) |
| `-q N` | Number of probes sent per hop (default 3) |
| `-I` | Use ICMP Echo Request probes |
| `-T` | Use TCP SYN probes |
| `-p PORT` | Destination port for UDP/TCP probes |
| `-w SEC` | How long to wait for a response before timing out a probe |
| `-4` / `-6` | Force IPv4 or IPv6 |

---

## Reading the Output

```
 1  192.168.1.1 (192.168.1.1)  1.203 ms  0.998 ms  1.102 ms
 2  10.10.0.1 (10.10.0.1)  8.221 ms  7.998 ms  8.451 ms
 3  * * *
 4  203.0.113.1 (isp-router.example.net)  14.552 ms  14.201 ms  14.998 ms
```

Each line is one hop, showing the responding router's address (and hostname, unless `-n` is used) and the round-trip time of each of the probes sent (usually 3). A row of `* * *` means no response was received within the timeout for that hop — this can mean a router is configured to silently drop or deprioritize the low-priority probe traffic (very common and not necessarily a real problem), or it can genuinely indicate packet loss at that point.

---

## What traceroute Is Actually Good For

- **Localizing where latency is introduced** — a sudden jump in response time at a specific hop usually points to a congested or distant link at that point in the path, rather than a problem with the destination server itself.
- **Confirming routing changes** — after a network reconfiguration, BGP change, or CDN migration, comparing traceroute paths before and after confirms traffic is taking the expected route.
- **Distinguishing "server down" from "network down"** — if the trace reaches close to the destination and then stops, the problem is likely near the destination network; if it fails early, the problem is likely local or with an upstream provider.
- **ISP troubleshooting evidence** — traceroute output is the standard artifact to hand to an ISP or hosting provider when reporting a suspected routing or peering issue.

---

## Important Caveats

- `* * *` hops are common and often harmless (rate-limiting of ICMP by routers), so don't assume they represent an outage.
- Because different probe types (UDP/ICMP/TCP) can be treated differently by firewalls along the path, a trace that appears to "die" partway through doesn't always mean packets aren't actually reaching the destination through a different protocol.
- Asymmetric routing means the path shown is only the *outbound* path — the return path packets take back to you can be completely different and isn't shown.

---

## Related Tools

- `tracert` — the Windows equivalent, using ICMP by default.
- `mtr` ("My Traceroute") — combines `traceroute` and continuous `ping`-style statistics into one live, continuously updating view — usually the better tool for real diagnosis.
- `ping` — simple reachability and round-trip time to a single destination, without hop-by-hop detail.
- `pathping` (Windows) — combines traceroute-style hop discovery with extended per-hop loss statistics.

---

## Example Walkthrough

```bash
traceroute -n example.com
mtr -rw example.com
```

Runs a quick numeric traceroute to see the hop-by-hop path, then follows up with `mtr` in report mode for a more statistically robust view — sending many more probes per hop and reporting average loss and latency, which is usually the more convincing evidence when escalating a suspected network problem.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)