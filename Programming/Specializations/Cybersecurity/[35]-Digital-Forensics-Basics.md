[Previous](./[34]-Incident-Response-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[36]-Authentication-Methods.md)

*Defensive Security*

# Lesson 35 - Digital Forensics Basics

## 35.1 What is Digital Forensics?

**Digital forensics** is the practice of collecting, preserving, and analyzing digital evidence in a way that's scientifically sound and legally defensible. It's used both during incident response (to understand exactly what happened) and in legal contexts (where evidence may need to hold up in court).

---

## 35.2 The Chain of Custody

The **chain of custody** is a documented record of who collected a piece of evidence, when, how it was stored, and who accessed it afterward. A broken or poorly documented chain of custody can make evidence inadmissible in legal proceedings, even if the evidence itself is genuine — this is why forensic handling procedures are followed strictly, not just as a formality.

---

## 35.3 Evidence Preservation Principles

- **Work from a copy, never the original** — investigators create a forensic **image** (an exact bit-for-bit copy) of a disk or memory, and perform all analysis on that copy, preserving the original untouched.
- **Hashing for integrity** (Lesson 16) — the original evidence and its copy are both hashed immediately, so investigators can later prove the copy wasn't altered during analysis.
- **Order of volatility** — some evidence disappears faster than others; RAM contents and active network connections are lost when a system powers off, while data on a disk persists much longer. Forensic collection generally prioritizes the most volatile evidence first.

---

## 35.4 Types of Digital Forensics

- **Disk forensics** — examining file systems, deleted files, and metadata on storage media.
- **Memory (RAM) forensics** — analyzing a system's volatile memory, which can reveal running processes, network connections, and malware that never touches disk at all.
- **Network forensics** — analyzing captured network traffic (Lesson 10) to reconstruct what data moved where.
- **Mobile forensics** — extracting and analyzing data from smartphones and tablets, which often have their own specialized tools due to unique file systems and security features.

---

## 35.5 Forensics in the Incident Response Process

Forensics feeds directly into the incident response lifecycle (Lesson 34): during **Identification**, forensic analysis confirms what actually happened; during **Eradication**, it helps ensure the full extent of a compromise (not just the visible parts) has been found and removed. Even organizations without dedicated in-house forensic capability benefit from understanding these principles, since preserving evidence correctly in the first hours after detecting an incident can make or break a later investigation by outside specialists.

[Previous](./[34]-Incident-Response-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[36]-Authentication-Methods.md)
