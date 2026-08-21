[Previous](./[9]-Firewalls-and-Network-Segmentation.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[11]-Linux-Security-Fundamentals.md)

*Networking Fundamentals for Security*

# Lesson 10 - Packet Analysis with Wireshark

## 10.1 What is Packet Analysis?

**Packet analysis** (or packet sniffing) means capturing and inspecting the raw data traveling across a network. It's used to troubleshoot connectivity problems, investigate suspicious traffic, verify that encryption is working correctly, and understand exactly what an application is sending. **Wireshark** is the most widely used free, open-source packet analysis tool.

Wireshark works by putting a network interface into **promiscuous mode**, which lets it capture all traffic visible on the network segment, not just traffic addressed to that specific device.

---

## 10.2 Capturing Traffic

To capture traffic, you select a network interface (e.g., Wi-Fi or Ethernet) and start a capture. Wireshark then displays every packet it sees in real time, including source/destination address, protocol, and a summary of the packet's contents.

Capture filters (applied before capturing, e.g., `port 443`) reduce the volume of data captured. Display filters (applied after capturing, e.g., `http.request` or `ip.addr == 192.168.1.5`) let you narrow down what's shown without discarding the rest of the capture.

---

## 10.3 Reading a Packet

Wireshark shows each packet broken into its protocol layers, mirroring the OSI model concepts from Lesson 7: Ethernet frame, IP header, TCP/UDP header, and the application-layer payload. This lets you inspect details like:

- The TCP three-way handshake (SYN, SYN-ACK, ACK) for a given connection.
- Plaintext credentials sent over an unencrypted protocol like HTTP or Telnet (a good demonstration of why encryption matters).
- DNS queries and responses, useful for spotting suspicious domain lookups.
- TLS handshake metadata (though the encrypted payload itself remains unreadable without the private key).

---

## 10.4 Practical Use Cases

- **Troubleshooting** — confirming whether a connection issue is a network problem or an application problem.
- **Security monitoring** — spotting unusual traffic patterns, like a host suddenly making many connections to an unfamiliar external IP (a possible sign of malware "phoning home").
- **Verifying encryption** — confirming that sensitive traffic (like login forms) is actually using HTTPS rather than falling back to HTTP.
- **Incident response and forensics** (Lesson 35) — packet captures (PCAP files) are frequently used as evidence when reconstructing what happened during an intrusion.

Because packet analysis can expose sensitive data, including other people's traffic, it should only ever be performed on networks you own or are explicitly authorized to monitor — the same authorization rule from Lesson 3 applies here.

[Previous](./[9]-Firewalls-and-Network-Segmentation.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[11]-Linux-Security-Fundamentals.md)
