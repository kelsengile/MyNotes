[Previous](./[2]-Setting-Up-a-Security-Lab.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[4]-The-CIA-Triad-and-Security-Principles.md)

*Getting Started*

# Lesson 3 - Legal & Ethical Considerations in Security Research

## 3.1 Why Authorization is Everything

The single most important rule in offensive security: **never test, scan, or attack a system you don't own or have explicit written permission to test.** The exact same command (e.g., a port scan or an exploit attempt) is a routine professional activity when authorized, and a criminal act when it isn't. Intent doesn't change the legal outcome — "I was just curious" or "I was trying to help" are not defenses.

Unauthorized access to computer systems is illegal in most countries, including under laws like the U.S. Computer Fraud and Abuse Act (CFAA) and the UK Computer Misuse Act. Penalties can include fines and imprisonment, even if no damage was caused.

---

## 3.2 Authorized Testing: Scope and Rules of Engagement

Professional penetration testers and researchers work under a **scope of engagement** — a written document (often part of a contract) that defines exactly which systems may be tested, what techniques are allowed, and what times testing may occur. Staying inside scope is a legal and ethical obligation, not just best practice.

Common frameworks for authorized testing include:

- **Bug bounty programs** — companies publicly invite researchers to find vulnerabilities in specifically listed systems, in exchange for recognition or payment.
- **Penetration testing contracts** — formal engagements with a signed **Rules of Engagement (RoE)** document.
- **Capture the Flag (CTF) competitions** and **practice platforms** (e.g., Hack The Box, TryHackMe) — legally sanctioned environments built specifically for practicing offensive techniques.

---

## 3.3 Responsible Disclosure

When a researcher finds a vulnerability in a real system, **responsible (or coordinated) disclosure** means privately reporting it to the affected organization first, giving them reasonable time to fix it before any public disclosure. This protects users from having a vulnerability exploited by criminals before a fix exists.

Publishing exploit details or attacking a system before disclosure, or without any disclosure at all, can cause real harm and carries legal risk, even if the researcher's intentions were good.

---

## 3.4 Professional Ethics

Beyond the law, cybersecurity professionals follow ethical norms:

- **Confidentiality** — client data and findings are not shared outside agreed channels.
- **Integrity** — findings are reported accurately, without exaggeration or fabrication.
- **Minimizing harm** — testing should avoid disrupting production systems where possible, and any accidental impact should be disclosed immediately.
- **Least privilege in testing** — testers use only the access needed to demonstrate a finding, not to explore beyond it.

Many professional certifications (e.g., those from (ISC)², CompTIA, and Offensive Security) require agreement to a formal code of ethics as a condition of certification.

[Previous](./[2]-Setting-Up-a-Security-Lab.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[4]-The-CIA-Triad-and-Security-Principles.md)
