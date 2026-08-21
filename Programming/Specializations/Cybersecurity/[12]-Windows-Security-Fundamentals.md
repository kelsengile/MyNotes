[Previous](./[11]-Linux-Security-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[13]-File-Permissions-and-Access-Control.md)

*Operating System Security*

# Lesson 12 - Windows Security Fundamentals

## 12.1 Why Windows Matters in Security

Windows dominates enterprise desktop environments and remains common on servers, making it a frequent target for attackers — and a core subject for defenders. Its security model differs meaningfully from Linux's, particularly around identity and centralized management.

---

## 12.2 Active Directory and Domains

**Active Directory (AD)** is Microsoft's directory service, used by most medium and large organizations to centrally manage users, computers, and permissions across a network, called a **domain**. Key concepts:

- **Domain Controller (DC)** — the server that holds the AD database and handles authentication for the domain; compromising a DC often means compromising the entire network.
- **Group Policy Objects (GPOs)** — centrally define and enforce settings (password policies, software restrictions, security configurations) across many machines at once.
- **Kerberos** — the authentication protocol AD uses, which issues time-limited tickets rather than repeatedly sending passwords, but is also the target of well-known attacks like "Kerberoasting" and "Golden Ticket" attacks.

Because a single compromised AD account can sometimes lead to full domain compromise, AD security is one of the most heavily studied areas in enterprise offensive and defensive security.

---

## 12.3 User Account Control (UAC) and Privilege Levels

**UAC** prompts a user for confirmation (or admin credentials) before allowing an action that requires elevated privileges, similar in spirit to Linux's `sudo`. Running as a standard user rather than an Administrator, and only elevating when necessary, significantly reduces the impact of malware — many malicious programs fail or are limited if they can't obtain admin rights.

---

## 12.4 Windows Security Tools and Logs

- **Windows Defender** — Microsoft's built-in antivirus and endpoint protection.
- **Windows Firewall** — host-based firewall included with Windows.
- **Event Viewer / Windows Event Logs** — records system, security, and application events (e.g., logon attempts, both successful and failed), critical for detection and investigation.
- **PowerShell** — a powerful scripting/administration shell; also a common tool abused by attackers for "living off the land" attacks (using legitimate built-in tools instead of custom malware, to avoid detection).

---

## 12.5 Basic Hardening Practices

- Apply updates promptly through Windows Update / WSUS.
- Enforce strong password and account lockout policies via Group Policy.
- Limit the number of Domain Admin accounts and avoid using them for routine tasks.
- Enable and centrally collect security event logs.
- Restrict or monitor PowerShell usage, since it's frequently abused by attackers already inside a network.

[Previous](./[11]-Linux-Security-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[13]-File-Permissions-and-Access-Control.md)
