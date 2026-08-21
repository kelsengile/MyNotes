[Previous](./[26]-Ransomware-and-Data-Exfiltration.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[28]-Reconnaissance-and-Information-Gathering.md)

*Offensive Security*

# Lesson 27 - Introduction to Penetration Testing

## 27.1 What is Penetration Testing?

**Penetration testing (pentesting)** is authorized, simulated attack against a system to identify exploitable vulnerabilities before real attackers do. Unlike a vulnerability scan (which identifies potential weaknesses), a penetration test actively attempts to exploit them, demonstrating real-world impact — as covered in Lesson 3, this always requires explicit written authorization.

---

## 27.2 Types of Penetration Tests

- **Black box** — the tester has no prior knowledge of the target's internal systems, simulating an external attacker starting from scratch.
- **White box** — the tester has full knowledge (source code, network diagrams, credentials), enabling a deeper, more thorough assessment in less time.
- **Gray box** — a middle ground, with partial knowledge (e.g., a standard user account), simulating an insider threat or a partially-informed attacker.

Tests are also categorized by scope: **network pentests**, **web application pentests**, **wireless pentests**, **physical pentests**, and **social engineering assessments**, among others.

---

## 27.3 The Penetration Testing Methodology

Most penetration tests follow a broadly similar structure:

1. **Pre-engagement** — defining scope, rules of engagement, and objectives with the client.
2. **Reconnaissance** — gathering information about the target (Lesson 28).
3. **Scanning and enumeration** — identifying live hosts, open ports, and running services (Lesson 29).
4. **Exploitation** — attempting to actively exploit identified weaknesses (Lesson 30).
5. **Post-exploitation** — assessing the actual impact of a successful exploit, such as privilege escalation (Lesson 31) or lateral movement.
6. **Reporting** — documenting findings, severity, evidence, and remediation recommendations for the client.

Reporting is often considered the most important deliverable — a vulnerability that isn't clearly communicated and prioritized won't get fixed, no matter how skillfully it was found.

---

## 27.4 Red Team vs. Penetration Test vs. Vulnerability Assessment

These terms are related but distinct:

- **Vulnerability assessment** — identifies and catalogs potential weaknesses, typically via automated scanning, without actively exploiting them.
- **Penetration test** — actively exploits vulnerabilities to demonstrate real impact, usually scoped and time-boxed, often with the defensive team aware testing is occurring.
- **Red team engagement** — a broader, more realistic, often longer-term simulated attack focused on achieving specific objectives (like reaching sensitive data) while evading detection, frequently without the defensive (blue) team's advance knowledge, to genuinely test detection and response capabilities.

[Previous](./[26]-Ransomware-and-Data-Exfiltration.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[28]-Reconnaissance-and-Information-Gathering.md)
