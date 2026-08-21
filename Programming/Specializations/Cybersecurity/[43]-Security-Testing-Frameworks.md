[Previous](./[42]-Vulnerability-Scanners.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[44]-Scripting-for-Security-Automation.md)

*Tools of the Trade*

# Lesson 43 - Security Testing Frameworks (Metasploit, Burp Suite)

## 43.1 Metasploit Framework

**Metasploit**, introduced briefly in Lesson 30, is an open-source exploitation framework widely used in authorized penetration testing. It provides a structured, modular library of exploits, payloads, and auxiliary tools, letting testers efficiently demonstrate the real-world impact of a vulnerability rather than writing exploit code from scratch for every engagement. Its modularity also makes it valuable for training and research in isolated lab environments (Lesson 2).

---

## 43.2 Burp Suite

**Burp Suite** is the industry-standard toolkit for web application security testing. It works primarily as an intercepting **proxy**, sitting between the tester's browser and the target web application, capturing and allowing modification of every request and response — letting testers directly observe and manipulate exactly what the application sends and receives.

Key Burp Suite components:

- **Proxy** — intercepts and allows manual inspection/modification of HTTP requests.
- **Repeater** — lets a tester resend a modified request repeatedly, useful for methodically probing a specific parameter.
- **Intruder** — automates sending many variations of a request, useful for tasks like brute-forcing or systematically testing many injection payloads.
- **Scanner** (in the commercial version) — automates detection of many common web vulnerabilities, complementing manual testing.

---

## 43.3 Other Notable Tools

- **OWASP ZAP (Zed Attack Proxy)** — a free, open-source alternative to Burp Suite, popular for both manual testing and automated scanning integrated into CI/CD pipelines (Lesson 40).
- **Kali Linux / Parrot OS** — Linux distributions pre-loaded with hundreds of security tools, widely used as the base operating system for penetration testing work (introduced in Lesson 2).
- **Wireshark** (Lesson 10) — while primarily a general packet analyzer, it's frequently used during security testing to verify what's actually happening on the wire.

---

## 43.4 Using These Tools Responsibly

Every tool in this lesson is dual-use: the same capability that helps a defender find and fix a flaw can cause real harm if used against a system without authorization. As emphasized throughout this course (Lesson 3), these tools should only ever be used against systems you own or have explicit written permission to test — most professional platforms and communities around these tools actively reinforce this ethical and legal norm.

[Previous](./[42]-Vulnerability-Scanners.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[44]-Scripting-for-Security-Automation.md)
