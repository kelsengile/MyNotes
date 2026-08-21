[Previous](./[1]-What-is-Cybersecurity.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[3]-Legal-and-Ethical-Considerations.md)

*Getting Started*

# Lesson 2 - Setting Up a Security Lab (VMs, Sandboxes)

## 2.1 Why You Need an Isolated Lab

Cybersecurity is a hands-on discipline, but practicing on real, unauthorized systems is illegal (see Lesson 3). A **lab** is an isolated environment where you can safely install vulnerable software, run malware samples, or practice exploitation techniques without risking your main computer, your network, or anyone else's systems.

A good lab is isolated from your host network, disposable (easy to reset if something breaks), and snapshot-friendly (so you can save and restore a known-good state).

---

## 2.2 Virtual Machines (VMs)

A **virtual machine** is a software-based emulation of a physical computer. A **hypervisor** (like VirtualBox, VMware Workstation, or Proxmox) lets you run multiple isolated operating systems on one physical machine.

Key concepts:

- **Snapshots** — save the exact state of a VM so you can revert after breaking something.
- **Isolated/Host-Only networking** — connects VMs to each other but not to your home network or the internet, preventing accidental exposure.
- **NAT networking** — gives VMs internet access while still isolating them from your host's local network.

A typical beginner lab includes a Linux distribution (e.g., Kali Linux or Parrot OS, which ship with security tools pre-installed) as an "attacker" machine, and one or more intentionally vulnerable VMs (e.g., Metasploitable) as "target" machines.

---

## 2.3 Sandboxes and Containers

A **sandbox** is a restricted environment used to run untrusted or potentially malicious code without letting it affect the host system. Sandboxes are especially important for malware analysis (Lesson 24), where you need to observe malicious behavior safely.

**Containers** (e.g., Docker) provide lighter-weight isolation than full VMs by sharing the host's kernel while isolating the file system and processes. Containers are fast to spin up and tear down, which makes them useful for quickly testing a vulnerable web app, but they offer weaker isolation than VMs — never use a container as your only defense against genuinely dangerous malware.

---

## 2.4 Building a Basic Practice Lab

A common beginner setup:

1. Install a hypervisor (VirtualBox is free and beginner-friendly).
2. Create an isolated, host-only virtual network.
3. Install an attacker VM (e.g., Kali Linux) on that network.
4. Install a deliberately vulnerable target VM (e.g., Metasploitable, OWASP Juice Shop, or DVWA) on the same isolated network.
5. Take a snapshot of each VM immediately after setup, before you start experimenting.

This setup lets you safely practice scanning, exploitation, and defensive monitoring techniques covered later in this course.

[Previous](./[1]-What-is-Cybersecurity.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[3]-Legal-and-Ethical-Considerations.md)
