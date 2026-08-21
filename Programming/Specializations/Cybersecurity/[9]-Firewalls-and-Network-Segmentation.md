[Previous](./[8]-TCP-IP-Ports-and-Protocols.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[10]-Packet-Analysis-with-Wireshark.md)

*Networking Fundamentals for Security*

# Lesson 9 - Firewalls & Network Segmentation

## 9.1 What is a Firewall?

A **firewall** is a system that monitors and controls incoming and outgoing network traffic based on a defined set of rules. Firewalls can be hardware appliances, software running on a server, or built into an operating system (e.g., Windows Defender Firewall, `iptables`/`nftables` on Linux).

Firewall rules are typically structured as an ordered list of allow/deny decisions based on source/destination IP, port, and protocol, ending in a **default-deny** policy — blocking everything not explicitly allowed, which is far more secure than a default-allow policy.

---

## 9.2 Types of Firewalls

- **Packet-filtering firewalls** — inspect individual packets against rules (source/destination IP, port) without tracking connection state.
- **Stateful firewalls** — track the state of active connections, so they can intelligently allow return traffic for a connection that was legitimately initiated, and block unsolicited traffic.
- **Next-Generation Firewalls (NGFW)** — add deeper inspection, such as identifying the actual application generating traffic (not just the port), intrusion prevention, and integration with threat intelligence.
- **Web Application Firewalls (WAF)** — operate at Layer 7, specifically inspecting HTTP traffic to block attacks like SQL injection or XSS aimed at web applications.

---

## 9.3 Network Segmentation

**Segmentation** means dividing a network into smaller, isolated zones so that a compromise in one zone doesn't automatically grant access to everything else. This limits an attacker's ability to move freely once they gain a foothold — a concept known as limiting **lateral movement**.

Common segmentation patterns:

- **DMZ (Demilitarized Zone)** — a buffer zone for public-facing servers (e.g., a web server), isolated from both the internet and the internal network, so that if the web server is compromised, the attacker still can't directly reach internal systems.
- **VLANs (Virtual LANs)** — logically separate a physical network into multiple isolated broadcast domains, often used to separate departments (e.g., HR, Finance, Guest Wi-Fi).
- **Micro-segmentation** — fine-grained isolation, often used in cloud/data-center environments, that restricts communication between individual workloads rather than just broad zones.

---

## 9.4 Defense in Depth

Firewalls and segmentation are examples of **defense in depth** — the principle that security shouldn't rely on any single control. If a firewall rule is misconfigured or bypassed, segmentation limits the damage; if segmentation fails, endpoint controls (Lesson 14) and monitoring (Lesson 32) provide additional layers. No single control should be treated as sufficient on its own.

[Previous](./[8]-TCP-IP-Ports-and-Protocols.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[10]-Packet-Analysis-with-Wireshark.md)
