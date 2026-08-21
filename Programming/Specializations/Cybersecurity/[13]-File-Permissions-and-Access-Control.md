[Previous](./[12]-Windows-Security-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[14]-System-Hardening.md)

*Operating System Security*

# Lesson 13 - File Permissions & Access Control

## 13.1 Access Control Models

**Access control** determines who can do what to a resource. There are a few common models:

- **DAC (Discretionary Access Control)** — the resource owner decides who gets access (e.g., standard Linux/Windows file permissions).
- **MAC (Mandatory Access Control)** — access is determined by system-wide policy that even the owner can't override (e.g., SELinux, AppArmor).
- **RBAC (Role-Based Access Control)** — permissions are assigned to roles (e.g., "Manager," "Auditor"), and users are assigned to roles, rather than managing permissions per individual.
- **ABAC (Attribute-Based Access Control)** — access decisions consider multiple attributes (user department, time of day, device type) for more dynamic, fine-grained control.

---

## 13.2 Linux File Permissions

Linux permissions are shown as a 10-character string, e.g., `-rwxr-xr--`:

- The first character indicates file type (`-` for regular file, `d` for directory).
- The next three groups of three represent permissions for **owner**, **group**, and **others**.
- Each group's permissions are **r**ead, **w**rite, and e**x**ecute.

Permissions can also be represented numerically (`chmod 754`), where read=4, write=2, execute=1, summed per group. `chmod 754` means owner=rwx(7), group=r-x(5), others=r--(4).

- `chown user:group file` changes ownership.
- **SUID/SGID** bits allow a program to run with the file owner's/group's privileges rather than the executing user's — powerful, but a common target for privilege escalation if misconfigured (Lesson 31).

---

## 13.3 Windows Access Control (NTFS Permissions)

Windows uses **NTFS permissions** and **Access Control Lists (ACLs)**, which are more granular than basic Linux permissions. Each file or folder has an ACL listing which users/groups have which permissions (Read, Write, Modify, Full Control, etc.). Permissions can be inherited from parent folders, and explicitly set permissions override inherited ones.

Windows also distinguishes **share permissions** (applied when accessing a folder over the network) from **NTFS permissions** (applied locally) — when both apply, the more restrictive combination wins.

---

## 13.4 The Principle of Least Privilege in Practice

Applying least privilege to file and system access means:

- Granting only the minimum access needed to perform a job function.
- Regularly reviewing and removing unnecessary access ("access creep" happens when employees change roles but retain old permissions).
- Separating duties, so no single account has enough access to cause major damage alone (e.g., the person who approves a payment shouldn't also be the one who can create vendor accounts).

Misconfigured or overly broad permissions are one of the most common root causes behind both external breaches and insider incidents.

[Previous](./[12]-Windows-Security-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[14]-System-Hardening.md)
