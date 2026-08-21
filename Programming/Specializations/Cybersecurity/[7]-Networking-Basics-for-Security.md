[Previous](./[6]-Common-Attack-Vectors-Overview.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[8]-TCP-IP-Ports-and-Protocols.md)

*Networking Fundamentals for Security*

# Lesson 7 - Networking Basics for Security Professionals

## 7.1 Why Networking Matters for Security

Nearly every attack travels across a network at some point — even malware that starts on a USB drive usually "phones home" over the internet. You can't secure, monitor, or investigate what you don't understand, which is why networking fundamentals come before most other technical security topics.

---

## 7.2 The OSI Model

The **OSI (Open Systems Interconnection) model** breaks network communication into seven conceptual layers. Security professionals use it as shared vocabulary for describing where a problem or attack occurs:

1. **Physical** — cables, radio signals, physical hardware.
2. **Data Link** — local delivery between directly connected devices (e.g., Ethernet, MAC addresses).
3. **Network** — routing data between networks (e.g., IP addresses).
4. **Transport** — reliable delivery between applications (e.g., TCP, UDP).
5. **Session** — managing connections/sessions between applications.
6. **Presentation** — data formatting and encryption (e.g., TLS).
7. **Application** — the protocols applications actually use (e.g., HTTP, DNS).

An attack like ARP spoofing happens at Layer 2, an IP spoofing attack at Layer 3, and a SQL injection attack at Layer 7 — this vocabulary helps teams communicate precisely.

---

## 7.3 IP Addresses and Subnetting Basics

An **IP address** uniquely identifies a device on a network. IPv4 addresses (e.g., `192.168.1.10`) are 32-bit numbers written as four decimal octets. Private IP ranges (like `192.168.0.0/16`, `10.0.0.0/8`) are reserved for internal networks and aren't routable on the public internet, which is part of why home and office networks use a router with **NAT (Network Address Translation)** to share one public IP address.

A **subnet mask** (e.g., `/24` or `255.255.255.0`) defines how many addresses belong to the same local network. Understanding subnetting helps security professionals reason about network segmentation (Lesson 9) and read tools like Nmap and Wireshark accurately.

---

## 7.4 DNS, MAC Addresses, and Basic Network Topology

- **MAC address** — a hardware-level identifier unique to a network interface, used for local delivery within a network segment.
- **DNS (Domain Name System)** — translates human-readable domain names (like `example.com`) into IP addresses. Because DNS is trusted so implicitly, it's a common attack target (e.g., DNS spoofing, DNS tunneling for data exfiltration).
- **Common topologies** — a typical corporate network includes a perimeter (facing the internet), a DMZ (a semi-isolated zone for public-facing servers), and an internal network (where sensitive systems live), separated by firewalls.

[Previous](./[6]-Common-Attack-Vectors-Overview.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[8]-TCP-IP-Ports-and-Protocols.md)
