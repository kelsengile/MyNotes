[Previous](./[5]-Threats-Vulnerabilities-and-Risk.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[7]-Networking-Basics-for-Security.md)

*Core Concepts*

# Lesson 6 - Common Attack Vectors Overview

## 6.1 What is an Attack Vector?

An **attack vector** is the path or method an attacker uses to gain unauthorized access to a system. Understanding common attack vectors helps defenders prioritize controls, and helps offensive practitioners understand where to look for weaknesses. This lesson is a high-level map — most of these vectors get their own dedicated lesson later in the course.

---

## 6.2 Human-Targeted Vectors

Humans are frequently the easiest target because they can be manipulated without needing to break any technical control:

- **Phishing** — fraudulent emails or messages tricking someone into revealing credentials or installing malware (Lesson 25).
- **Social engineering** — broader psychological manipulation, such as pretexting (impersonating someone trustworthy) or baiting (leaving infected USB drives to be found).
- **Credential theft/reuse** — attackers use stolen or leaked passwords, betting that people reuse the same password across multiple sites.

---

## 6.3 Network and System-Targeted Vectors

- **Unpatched software** — attackers exploit publicly known vulnerabilities in software that hasn't been updated.
- **Misconfiguration** — default passwords, unnecessary open ports, or overly permissive access rules.
- **Malware** — malicious software delivered via email attachments, infected downloads, or compromised websites (Lesson 24).
- **Man-in-the-Middle (MitM)** — intercepting communication between two parties, often on insecure networks like public Wi-Fi.
- **Denial-of-Service (DoS/DDoS)** — overwhelming a system with traffic or requests so legitimate users can't access it.

---

## 6.4 Application-Targeted Vectors

- **Injection attacks** — inserting malicious input (e.g., SQL) that an application executes unintentionally (Lesson 20).
- **Cross-Site Scripting (XSS)** — injecting malicious scripts into web pages viewed by other users (Lesson 21).
- **Broken authentication** — weak login or session-handling logic that lets attackers bypass or hijack accounts (Lesson 22).

---

## 6.5 Supply Chain and Physical Vectors

- **Supply chain attacks** — compromising a trusted third-party vendor or software dependency to reach the real target indirectly (e.g., inserting malicious code into a widely used software library).
- **Physical access** — walking into a facility, plugging in a rogue device, or stealing an unlocked laptop.

Most real-world breaches combine multiple vectors — for example, a phishing email (human vector) that delivers malware (technical vector), which then exploits a missing patch (system vector) to spread further.

[Previous](./[5]-Threats-Vulnerabilities-and-Risk.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[7]-Networking-Basics-for-Security.md)
