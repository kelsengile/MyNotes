[Previous](./[28]-Reconnaissance-and-Information-Gathering.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[30]-Exploitation-Basics.md)

*Offensive Security*

# Lesson 29 - Scanning & Enumeration

## 29.1 From Recon to Scanning

While reconnaissance (Lesson 28) gathers broad, often indirect information, **scanning** actively probes a target's systems to determine what's actually running and reachable — building a technical map of the attack surface.

---

## 29.2 Host Discovery and Port Scanning

**Host discovery** identifies which systems on a network are live and responding (e.g., via ping sweeps). **Port scanning** then probes those live hosts to determine which ports are open, and therefore which services might be running.

**Nmap** is the most widely used port scanning tool. Common scan types include:

- **TCP Connect scan** — completes the full TCP three-way handshake (Lesson 8); reliable but easily logged.
- **SYN scan ("half-open" scan)** — sends a SYN packet and analyzes the response without completing the handshake, making it faster and stealthier.
- **UDP scan** — checks for open UDP ports, generally slower and less reliable than TCP scanning due to UDP's connectionless nature.

Scan results typically classify ports as **open**, **closed**, or **filtered** (blocked by a firewall, with no clear response either way).

---

## 29.3 Service and Version Enumeration

Beyond just knowing a port is open, **enumeration** identifies exactly *what* is running on it — for example, not just "port 22 is open" but "OpenSSH 8.2 is running on port 22." This is valuable because specific software versions can be checked against known vulnerability databases (Lesson 48). Techniques include:

- **Banner grabbing** — connecting to a service and reading the identifying information it returns.
- **Service fingerprinting** — tools like Nmap can send probes and compare responses against a database of known service signatures.

---

## 29.4 Enumerating Specific Services

Different services require different enumeration approaches:

- **SMB enumeration** — identifying shared folders, usernames, and OS version on Windows systems.
- **Web enumeration** — discovering hidden directories, files, and technologies on a web server (e.g., using tools that brute-force common file/directory names).
- **DNS enumeration** — discovering subdomains and DNS records associated with a target domain.

---

## 29.5 Why Enumeration is Critical

A thorough enumeration phase directly informs the exploitation phase (Lesson 30): attackers and testers alike can't exploit what they don't know exists. This is also why defenders focus heavily on minimizing exposed services and suppressing unnecessary banner/version information (though this alone, known as "security through obscurity," should never be relied on as a primary defense).

[Previous](./[28]-Reconnaissance-and-Information-Gathering.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[30]-Exploitation-Basics.md)
