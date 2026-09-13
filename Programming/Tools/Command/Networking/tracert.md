[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tracert

`tracert` is the Windows built-in equivalent of `traceroute` — it maps the hop-by-hop path packets take across the network to reach a destination, using ICMP Echo Requests instead of the UDP probes Unix-like `traceroute` sends by default.

Reference: [https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/tracert](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/tracert)

---

## What Is tracert?

Like its Unix counterpart, `tracert` relies on the IP TTL (Time To Live) mechanism: it sends a series of ICMP Echo Request packets with increasing TTL values, and each router along the path that discards an expired packet sends back an ICMP "Time Exceeded" reply identifying itself. By increasing the TTL by one each round, `tracert` reveals the full chain of routers, one hop at a time, until a packet reaches the destination.

`tracert` has shipped with every version of Windows since Windows NT/95, is run from Command Prompt or PowerShell, and requires no separate installation — unlike `traceroute`, which on some Unix systems needs to be installed from a package.

---

## Core Commands

```
tracert example.com                 # Trace the route to a host
tracert -d example.com              # Skip reverse DNS lookups (faster, shows IPs only)
tracert -h 15 example.com           # Limit to 15 maximum hops
tracert -w 500 example.com          # Set the per-hop timeout to 500 milliseconds
tracert -4 example.com              # Force IPv4
tracert -6 example.com              # Force IPv6
```

## Flag Reference

| Flag | Meaning |
|---|---|
| `-d` | Don't resolve hostnames, numeric addresses only |
| `-h N` | Maximum number of hops to try |
| `-w N` | Timeout per reply, in milliseconds |
| `-4` | Force IPv4 |
| `-6` | Force IPv6 |
| `-j HOST-LIST` | (IPv4 only) Loose source route along the given hosts |

---

## Reading the Output

```
Tracing route to example.com [93.184.216.34]
over a maximum of 30 hops:

  1     1 ms     1 ms     1 ms  192.168.1.1
  2     8 ms     7 ms     8 ms  10.10.0.1
  3     *        *        *     Request timed out.
  4    15 ms    14 ms    15 ms  isp-router.example.net [203.0.113.1]
```

Each row is a hop, with three round-trip time samples (three probes are sent per hop by default). `Request timed out.` on a row means no reply arrived within the timeout window for that hop — this is common at routers configured to deprioritize or ignore ICMP, and doesn't necessarily mean the connection is broken further along.

---

## Why ICMP Instead of UDP?

Windows' `tracert` uses ICMP Echo Request/Reply exclusively, mirroring the mechanism `ping` uses. This is a meaningful difference from Unix `traceroute`'s default UDP probes: because many firewalls and routers treat ICMP and UDP traffic differently, running a trace to the same destination from a Windows machine versus a Linux machine can produce a different-looking path or different points of failure — neither is "more correct," they're just testing with different protocols.

---

## What tracert Is Used to Diagnose

- **Finding where a connection stalls** — a jump in latency or a run of timeouts at a specific hop narrows down whether the problem is local, with the ISP, or somewhere out on the wider internet.
- **VPN and routing verification** — confirming traffic is actually leaving through a VPN tunnel (or not) by inspecting the early hops.
- **Reporting connectivity issues to an ISP or hosting provider** — `tracert` output is standard supporting evidence when opening a network support ticket.
- **Comparing paths to different destinations** — spotting whether a slowdown is destination-specific or affects everything (pointing at a local/ISP issue instead).

---

## Common Pitfalls

- Timeouts (`Request timed out.`) partway through a trace are frequently caused by routers rate-limiting or dropping ICMP, not by an actual routing failure — if the destination itself is reachable via `ping` or a browser, don't over-interpret mid-path timeouts.
- Corporate or public Wi-Fi networks often block ICMP entirely on some segments, which can make `tracert` look broken even when normal browsing works fine.
- `tracert` only shows the outbound path; the return path can differ due to asymmetric routing, and isn't visible in the output.

---

## Related Tools

- `traceroute` — the Unix/Linux/macOS equivalent, defaulting to UDP probes (though it can be told to use ICMP with `-I`).
- `pathping` — a Windows tool that combines traceroute-style hop discovery with extended statistics on packet loss per hop, gathered over a longer sampling window.
- `ping` — basic reachability and latency to a single destination, no hop detail.
- `nslookup` / `Resolve-DnsName` — confirm a hostname resolves to the expected IP before tracing to it.

---

## Example Walkthrough

```
tracert -d example.com
pathping example.com
```

Runs a quick numeric trace to see the hop-by-hop path, then follows up with `pathping`, which re-probes every hop repeatedly over a longer window to produce reliable per-hop packet-loss percentages — much stronger evidence than a single `tracert` run when trying to prove where loss is occurring.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)