[Previous](./[10]-Packet-Analysis-with-Wireshark.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[12]-Windows-Security-Fundamentals.md)

*Operating System Security*

# Lesson 11 - Linux Security Fundamentals

## 11.1 Why Linux Matters in Security

Linux runs the vast majority of internet-facing servers, cloud infrastructure, and security tools themselves (most penetration testing distributions, like Kali Linux, are Linux-based). Whether you're defending servers or attacking them in an authorized test, comfort with Linux is essential.

---

## 11.2 Users, Groups, and Privilege

Linux is a multi-user system built around the principle of **least privilege** — each account should have only the access it needs. Key concepts:

- **root** — the superuser account with unrestricted system access. Well-managed systems avoid logging in as root directly, using `sudo` instead to grant temporary elevated privileges for specific commands, which also creates an audit trail.
- **Users and groups** — every file and process is owned by a user and a group, which determines what actions are permitted.
- **`sudo`** — allows a permitted user to run a command as another user (usually root), governed by rules in `/etc/sudoers`.

---

## 11.3 The Linux File System and Key Security Files

Understanding a few key files is core to Linux security:

- `/etc/passwd` — stores user account information (username, user ID, home directory). Readable by all users but doesn't store actual passwords.
- `/etc/shadow` — stores hashed passwords, readable only by root, which is why the separation from `/etc/passwd` matters for security.
- `/var/log/` — contains system and application logs, essential for detecting and investigating incidents.
- `/etc/ssh/sshd_config` — configures the SSH server; common hardening steps include disabling root login and password authentication in favor of key-based authentication.

---

## 11.4 Essential Security Commands

A few commands come up constantly in Linux security work:

- `ps aux` / `top` — view running processes, useful for spotting suspicious activity.
- `netstat -tulnp` / `ss -tulnp` — view active network connections and listening ports.
- `chmod` / `chown` — change file permissions and ownership (covered in depth in Lesson 13).
- `systemctl` — manage services, including disabling unnecessary ones to reduce attack surface.
- `grep`, `find` — search logs and the filesystem, frequently used during investigations.

---

## 11.5 Basic Hardening Practices

- Keep the system updated (`apt update && apt upgrade`, `yum update`, etc.) to patch known vulnerabilities.
- Disable or remove unused services to reduce attack surface.
- Use SSH key-based authentication instead of passwords, and disable direct root SSH login.
- Apply the principle of least privilege to user accounts and `sudo` rules.
- Enable and review logging (Lesson 32 covers this in more depth).

[Previous](./[10]-Packet-Analysis-with-Wireshark.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[12]-Windows-Security-Fundamentals.md)
