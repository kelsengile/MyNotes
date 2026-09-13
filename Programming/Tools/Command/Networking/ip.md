[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# ip

`ip` is the modern Linux command for viewing and configuring networking — addresses, interfaces, routing tables, and more. It's part of the `iproute2` package and has largely replaced the older `ifconfig`/`route`/`arp` trio, which are deprecated on most current distributions.

Reference: [https://man7.org/linux/man-pages/man8/ip.8.html](https://man7.org/linux/man-pages/man8/ip.8.html)

---

## What Is ip?

`ip` is an "object, command" style tool: every invocation names an **object** (`address`, `link`, `route`, `neigh`, etc.) and an **action** on it (`show`, `add`, `del`, `set`). This structure is more consistent and far more capable than the older `ifconfig`-era tools, which each handled only one narrow slice of networking configuration. `ip` talks to the kernel's networking stack through Netlink sockets rather than the older ioctl interface `ifconfig` used, which is part of why it supports modern features (VLANs, network namespaces, policy routing) those older tools never gained.

---

## Core Commands

```bash
ip addr show                    # List all interfaces and their IP addresses (shorthand: ip a)
ip link show                    # List network interfaces and their state (shorthand: ip l)
ip route show                   # Show the routing table (shorthand: ip r)
ip neigh show                   # Show the ARP/neighbor table (replaces `arp -a`)
ip addr add 192.168.1.50/24 dev eth0   # Assign an IP address to an interface
ip link set eth0 up             # Bring an interface up
ip link set eth0 down           # Bring an interface down
ip route add default via 192.168.1.1  # Set a default gateway
ip -s link show eth0            # Show interface statistics (packets, errors, drops)
ip -c a                         # Colorized output
```

## Object/Action Reference

| Object | Common actions | Purpose |
|---|---|---|
| `address` (`a`) | `show`, `add`, `del` | IP addresses assigned to interfaces |
| `link` (`l`) | `show`, `set`, `add`, `del` | Network interfaces themselves (state, MTU, MAC) |
| `route` (`r`) | `show`, `add`, `del`, `get` | The kernel routing table |
| `neigh` | `show`, `add`, `del`, `flush` | ARP/NDP neighbor cache |
| `netns` | `list`, `add`, `del`, `exec` | Network namespaces (isolated network stacks) |
| `tunnel` | `show`, `add`, `del` | Tunnel interfaces (GRE, IPIP, etc.) |
| `rule` | `show`, `add`, `del` | Policy routing rules |
| `maddr` | `show` | Multicast group membership |

---

## Reading `ip addr show` Output

```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP
    link/ether 08:00:27:aa:bb:cc brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.50/24 brd 192.168.1.255 scope global eth0
    inet6 fe80::a00:27ff:feaa:bbcc/64 scope link
```

- `2:` — the kernel's interface index.
- `<BROADCAST,MULTICAST,UP,LOWER_UP>` — interface flags; `UP` means administratively enabled, `LOWER_UP` means the physical link is actually connected.
- `link/ether` — the MAC address.
- `inet` / `inet6` — the assigned IPv4/IPv6 addresses with their subnet prefix length (`/24`, `/64`).

---

## Beyond Basic Address Viewing

- **Network namespaces (`ip netns`)**: create fully isolated virtual network stacks, each with their own interfaces, routes, and firewall rules — the underlying mechanism containers (Docker, Kubernetes pods) use to give each container its own private networking.
- **Virtual interfaces**: `ip link add` can create VLANs (`type vlan`), bridges (`type bridge`), bonds, and veth pairs (virtual Ethernet cables used to connect namespaces/containers to the host).
- **Policy-based routing (`ip rule` + multiple routing tables)**: route traffic differently based on source address, mark, or other criteria — used for multi-homed servers or VPN split-tunneling.
- **Traffic monitoring**: `ip -s link` shows per-interface RX/TX packet, byte, error, and drop counters, useful for diagnosing packet loss or a saturated NIC.
- **Neighbor management**: `ip neigh` inspects and manipulates the ARP (IPv4) and NDP (IPv6) caches that map IP addresses to MAC addresses on the local network segment.

---

## ip vs. the Old Tools

| Old tool | ip equivalent |
|---|---|
| `ifconfig` | `ip addr`, `ip link` |
| `route` | `ip route` |
| `arp` | `ip neigh` |
| `iptunnel` | `ip tunnel` |

The old tools are still present on many systems for compatibility but are considered legacy; `ip` is the maintained, actively developed interface and the only one that exposes newer kernel networking features.

---

## Related Tools

- `ss` — modern replacement for `netstat`, shows socket/connection state.
- `nmcli` / `nmtui` — higher-level NetworkManager tools for typical desktop network configuration.
- `bridge` — a sibling `iproute2` tool specifically for managing bridge devices and forwarding databases.
- `tc` — traffic control, for shaping/limiting bandwidth on interfaces managed via `ip link`.

---

## Example Walkthrough

```bash
ip addr show eth0
ip route show
ip route add default via 192.168.1.1 dev eth0
```

Checks the current IP configuration of a specific interface, reviews the existing routing table, then adds a default gateway route — a typical manual network setup sequence you might run on a fresh server or inside a container namespace before DHCP or a configuration management tool takes over.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)