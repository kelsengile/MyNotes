[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# tracert

`tracert` is the Windows command for tracing the network path — the sequence of routers, or "hops" — that packets take to reach a destination. It's the Windows equivalent of the Unix `traceroute` command.

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/tracert](https://learn.microsoft.com/windows-server/administration/windows-commands/tracert)

---

## What Is tracert?

`ping` tells you whether a destination is reachable; `tracert` tells you *how* your traffic gets there, hop by hop, and how long each hop takes. This is invaluable for figuring out where along a network path a slowdown or failure is actually happening.

---

## Core Commands

```cmd
tracert example.com          # Trace the route to a host, showing each hop's latency
tracert -d example.com       # Skip reverse DNS lookups (much faster)
tracert -h 15 example.com    # Limit the trace to 15 hops
```

---

## Reading the Output

```
  1     1 ms     1 ms     1 ms  192.168.1.1
  2    12 ms    11 ms    12 ms  10.10.0.1
  3     *        *        *     Request timed out.
  4    34 ms    33 ms    35 ms  203.0.113.1
```

Each row is one hop, with three latency samples. A `*` means that particular hop didn't respond — common for routers configured to ignore trace requests, and not automatically a sign of a broken connection if later hops still respond normally.

---

## Example Walkthrough

```cmd
tracert -d example.com
```

Traces the path to `example.com` with DNS lookups disabled for speed, letting you quickly spot which hop introduces a large jump in latency.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
