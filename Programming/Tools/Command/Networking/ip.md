[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# ip

`ip` is the modern Linux command for configuring and inspecting network interfaces, IP addresses, routing tables, and more. It replaces the older, now-deprecated `ifconfig` and `route` commands on most current distributions.

Download: [https://man7.org/linux/man-pages/man8/ip.8.html](https://man7.org/linux/man-pages/man8/ip.8.html)

---

## What Is ip?

`ip` is organized around **objects** — `link` (network interfaces), `addr` (IP addresses), `route` (routing table), and more — each with its own set of sub-commands. This is a different style from older single-purpose tools, but it's more consistent once you know the pattern: `ip <object> <command>`.

---

## Core Commands

```bash
ip addr show               # Show IP addresses for all interfaces (short: ip a)
ip link show                # Show network interfaces and their state (short: ip l)
ip route show                # Show the routing table (short: ip r)
ip addr add 192.168.1.50/24 dev eth0   # Manually assign an IP address
ip link set eth0 up          # Bring an interface up
ip link set eth0 down        # Bring an interface down
```

---

## ip vs. ifconfig

`ifconfig` only shows and configures IP addresses and basic interface state. `ip` covers that and more — routing, VLANs, network namespaces, tunnels — through one consistent tool, which is why it's the modern default even though `ifconfig` output is still familiar to many admins.

---

## Example Walkthrough

```bash
ip addr show
ip route show
ip link set wlan0 down
ip link set wlan0 up
```

Checks current IP addresses and routes, then power-cycles a Wi-Fi interface entirely from the command line — a quick fix for an adapter that's stopped responding.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
