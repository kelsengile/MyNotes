[Previous](./[30]-Exploitation-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[32]-Security-Monitoring-and-SIEM-Basics.md)

*Offensive Security*

# Lesson 31 - Privilege Escalation Basics

## 31.1 What is Privilege Escalation?

**Privilege escalation** is the process of gaining higher-level access than what was initially obtained — for example, going from a low-privileged user account to full administrator/root access. Initial exploitation (Lesson 30) often only grants limited access; privilege escalation is frequently necessary before an attacker or tester can achieve meaningful impact, such as accessing sensitive data or installing persistent malware.

---

## 31.2 Vertical vs. Horizontal Escalation

- **Vertical privilege escalation** — moving from a lower privilege level to a higher one (e.g., regular user to administrator).
- **Horizontal privilege escalation** — gaining access to another account at the *same* privilege level (e.g., accessing a different regular user's data), often related to the broken access control issues discussed in Lesson 19.

---

## 31.3 Common Privilege Escalation Techniques

- **Misconfigured permissions** — files, services, or scheduled tasks that run with elevated privileges but are writable by a lower-privileged user, letting them insert malicious code that runs with those higher privileges.
- **Unpatched local vulnerabilities** — kernel or OS-level bugs that grant elevated access to a local user.
- **Weak service configurations** — services running as a highly privileged account unnecessarily.
- **Credential exposure** — finding cached credentials, config files with embedded passwords, or password reuse that grants access to a more privileged account.
- **Exploiting SUID binaries on Linux** (Lesson 13) — programs that run with the file owner's privileges, which can be abused if misconfigured or vulnerable.
- **Token manipulation on Windows** — abusing how Windows manages access tokens to impersonate a more privileged user.

---

## 31.4 Lateral Movement and Persistence

Once elevated access is achieved on one system, attackers often pursue:

- **Lateral movement** — using the compromised system as a foothold to access other systems on the network, gradually expanding their reach (which is exactly what network segmentation from Lesson 9 is designed to slow down).
- **Persistence** — establishing a way to maintain access even if the initial entry point is discovered and closed, such as creating a new account, scheduled task, or backdoor.

---

## 31.5 Defensive Takeaways

- Apply least privilege rigorously (Lesson 13) — minimize the number of accounts and services with elevated rights.
- Regularly patch operating systems, since many escalation techniques rely on known, fixable vulnerabilities.
- Audit file and service permissions for unintentionally excessive access.
- Monitor for unusual privilege changes or new administrative accounts, which are strong indicators of an active compromise (Lesson 32 covers monitoring in depth).

[Previous](./[30]-Exploitation-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[32]-Security-Monitoring-and-SIEM-Basics.md)
