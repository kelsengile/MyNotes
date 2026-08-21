[Previous](./[7]-Networking-Basics-for-Security.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[9]-Firewalls-and-Network-Segmentation.md)

*Networking Fundamentals for Security*

# Lesson 8 - TCP/IP, Ports & Protocols

## 8.1 The TCP/IP Model

The **TCP/IP model** is the practical, four-layer model that the real internet runs on (simpler than the seven-layer OSI model): Network Access, Internet, Transport, and Application. In everyday security work, most conversations focus on the Internet layer (IP) and Transport layer (TCP/UDP).

---

## 8.2 TCP vs. UDP

- **TCP (Transmission Control Protocol)** is connection-oriented: it performs a **three-way handshake** (SYN, SYN-ACK, ACK) before sending data, and guarantees delivery and ordering. This reliability makes it the choice for web browsing, email, and file transfer — but it's slower than UDP.
- **UDP (User Datagram Protocol)** is connectionless: it sends data without confirming delivery. It's faster but unreliable, making it suited to use cases like video streaming, DNS lookups, and online gaming, where speed matters more than guaranteed delivery.

Attackers exploit the TCP handshake in **SYN flood** attacks, sending many SYN packets without completing the handshake to exhaust a server's connection resources — a classic Denial-of-Service technique.

---

## 8.3 Ports and Common Services

A **port** is a numbered endpoint (0–65535) that identifies which application or service on a device should receive incoming traffic. Well-known ports include:

| Port | Protocol | Service |
|---|---|---|
| 20/21 | TCP | FTP (file transfer) |
| 22 | TCP | SSH (secure remote login) |
| 23 | TCP | Telnet (unencrypted remote login — insecure) |
| 25 | TCP | SMTP (email sending) |
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP (unencrypted web) |
| 443 | TCP | HTTPS (encrypted web) |
| 445 | TCP | SMB (Windows file sharing) |
| 3389 | TCP | RDP (Windows remote desktop) |

Knowing common ports lets a security professional quickly interpret a port scan: an internet-facing server with port 23 (Telnet) or 3389 (RDP) open is often a red flag worth investigating.

---

## 8.4 Common Application Protocols

- **HTTP/HTTPS** — web traffic; HTTPS wraps HTTP in TLS encryption (Lesson 17).
- **SMTP/IMAP/POP3** — sending and retrieving email.
- **FTP/SFTP** — file transfer; plain FTP sends credentials unencrypted, while SFTP (SSH-based) encrypts them.
- **SSH** — encrypted remote administration of servers.
- **SMB** — Windows file and printer sharing; historically the target of major worms (e.g., WannaCry exploited an SMB vulnerability).

Understanding what a protocol is *supposed* to carry makes it much easier to spot when it's being abused — for example, DNS traffic (normally short lookups) suddenly carrying large volumes of data can indicate DNS tunneling used to exfiltrate stolen data.

[Previous](./[7]-Networking-Basics-for-Security.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[9]-Firewalls-and-Network-Segmentation.md)
