[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# ping

`ping` is a network diagnostic command used to test whether a remote host is reachable, and to measure how long a round trip to it takes. It's usually the very first tool people reach for when troubleshooting "is the internet down?"

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/ping](https://learn.microsoft.com/windows-server/administration/windows-commands/ping)

---

## What Is ping?

`ping` sends small ICMP "echo request" packets to a target and waits for "echo reply" packets back. Getting replies means the host is reachable and responding; no replies (or timeouts) point to a network, routing, or firewall problem somewhere between you and the destination.

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

## Reading the Output

```
64 bytes from example.com: icmp_seq=1 ttl=56 time=13.2 ms
```

`time` is the round-trip latency in milliseconds. `ttl` (Time To Live) roughly indicates how many network hops the packet took to arrive — a much lower TTL than expected can hint at an unusual routing path.

---

## Example Walkthrough

```bash
ping -c 4 8.8.8.8
ping -c 4 example.com
```

Pinging a known-reliable IP address (Google's public DNS) first checks whether your own internet connection works. Pinging the domain afterward checks whether DNS resolution and the specific site are the actual problem.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
