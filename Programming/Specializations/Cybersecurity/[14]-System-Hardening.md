[Previous](./[13]-File-Permissions-and-Access-Control.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[15]-Cryptography-Fundamentals.md)

*Operating System Security*

# Lesson 14 - System Hardening

## 14.1 What is Hardening?

**Hardening** is the process of reducing a system's **attack surface** — the sum of all points where an attacker could potentially interact with or exploit it. A freshly installed operating system typically ships with unnecessary services, default accounts, and permissive settings that make it easier to use out of the box, but also easier to attack. Hardening trades some of that convenience for security.

---

## 14.2 Reducing Attack Surface

Core hardening steps that apply broadly across operating systems:

- **Remove/disable unnecessary services and software** — every running service is a potential entry point.
- **Close unused ports** — if a service isn't needed externally, it shouldn't be reachable externally.
- **Change or disable default accounts and passwords** — many breaches exploit default credentials that were never changed.
- **Apply the principle of least privilege** to accounts, services, and file permissions.
- **Disable unused protocols** — e.g., disabling legacy, insecure protocols like Telnet or SMBv1.

---

## 14.3 Patch Management

Unpatched software is one of the most common causes of successful attacks, because exploit code for known vulnerabilities is often published publicly shortly after a patch is released (attackers reverse-engineer the patch to find what it fixes). A sound patch management process includes:

- Regularly checking for and testing updates before deployment.
- Prioritizing critical/high-severity patches, especially those with known active exploitation.
- Having a rollback plan in case a patch breaks something.
- Tracking patch status across the whole fleet, not just individual machines.

---

## 14.4 Security Baselines and Benchmarks

Rather than hardening from scratch, most organizations start from an established **baseline** — a documented, tested set of secure configuration settings. Common sources include:

- **CIS Benchmarks** (Center for Internet Security) — detailed, freely available hardening guides for specific operating systems and software.
- **DISA STIGs** (Security Technical Implementation Guides) — used heavily in U.S. government and defense contexts.
- **Vendor hardening guides** — e.g., Microsoft's own security baselines for Windows.

Using a recognized baseline also makes it easier to demonstrate compliance during audits (Lesson 46).

---

## 14.5 Endpoint Protection

Modern hardening also includes endpoint-level security software:

- **Antivirus/Anti-malware** — detects known malicious files via signatures and heuristics.
- **EDR (Endpoint Detection and Response)** — goes further than traditional antivirus, continuously monitoring endpoint behavior to detect and respond to suspicious activity, even from previously unseen threats.
- **Host-based firewalls** — control traffic to/from the individual machine, complementing network firewalls (Lesson 9).

[Previous](./[13]-File-Permissions-and-Access-Control.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[15]-Cryptography-Fundamentals.md)
