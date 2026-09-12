[Previous](./[15]-Disk-Scheduling.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[17]-Virtualization.md)

*Security And Advanced Topics*

# Lesson 16 - Protection And Security

## 16.1 Goals Of Protection

Protection refers to the mechanisms an OS uses to control which processes and users can access which resources — memory, files, devices, and each other. The goal is to prevent unintentional interference (a buggy program accidentally corrupting another's memory) as well as intentional misuse (a malicious program trying to read data it has no business accessing). Protection is enforced through the mechanisms covered earlier in this Topic, like dual-mode CPU operation and private virtual address spaces, combined with explicit permission systems layered on top of files and other resources.

| Threat type | Example |
|---|---|
| Unintentional interference | A buggy app writes past its own array bounds into another process's memory |
| Intentional misuse | A malicious app tries to read another user's private files |

---

## 16.2 Authentication And Access Control

Security starts with authentication — verifying that a user or process is who it claims to be, typically through passwords, cryptographic keys, biometrics, or multi-factor schemes combining several of these. Once a user is authenticated, access control determines what they're allowed to do. Common models include **discretionary access control**, where a resource's owner decides who else can access it (as with typical file permissions), and **role-based access control**, where permissions are assigned to roles and users are granted roles rather than individual permissions, simplifying management in large systems with many users.

**Example DAC (Unix-style file permissions):**

```
-rw-r--r--  1 alice  staff   report.docx
```

Alice (the owner) can read and write; everyone else in her group and the world can only read — a decision she made herself, which is the defining trait of discretionary access control.

---

## 16.3 Common Security Threats

Operating systems face a range of recurring threats. **Malware** — viruses, worms, trojans, and ransomware — is software designed to damage, steal from, or take control of a system, often by exploiting bugs or tricking users into running it. **Buffer overflows** occur when a program writes more data into a fixed-size memory buffer than it can hold, potentially overwriting adjacent memory and, in the worst case, letting an attacker inject and execute their own code. **Privilege escalation** attacks exploit bugs or misconfigurations to gain higher permissions than intended, such as a compromised user-space process gaining kernel-level access. Understanding these threat categories is the basis for the defenses discussed next.

| Threat | What it exploits | Typical impact |
|---|---|---|
| Malware | User trust / software bugs | Data theft, damage, ransom |
| Buffer overflow | Missing bounds checking | Code execution, crashes |
| Privilege escalation | Bugs or misconfiguration | Attacker gains higher access than intended |

---

## 16.4 Security Mechanisms

Modern operating systems layer several defenses to reduce the impact of these threats. **Sandboxing** confines a program's access to a restricted subset of system resources, limiting the damage it can do even if compromised. **Address Space Layout Randomization (ASLR)** randomizes where a process's code and data are placed in memory, making it harder for an attacker to reliably exploit memory-corruption bugs. **Encryption** protects data at rest and in transit so that even if it's accessed without authorization, it remains unreadable without the right key. **Least privilege**, a guiding principle rather than a single mechanism, means giving every process and user only the minimum access they need to do their job, limiting how much damage any single compromise can cause.

| Mechanism | Defends against | How |
|---|---|---|
| Sandboxing | Compromised apps | Restricts what resources they can touch |
| ASLR | Memory-corruption exploits | Randomizes memory layout |
| Encryption | Unauthorized data access | Makes stolen data unreadable |
| Least privilege | Broad damage from any single breach | Minimizes each process's granted access |

---

[Previous](./[15]-Disk-Scheduling.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[17]-Virtualization.md)