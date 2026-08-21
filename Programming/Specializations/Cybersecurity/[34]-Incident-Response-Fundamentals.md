[Previous](./[33]-Intrusion-Detection-and-Prevention-Systems.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[35]-Digital-Forensics-Basics.md)

*Defensive Security*

# Lesson 34 - Incident Response Fundamentals

## 34.1 What is Incident Response?

**Incident response (IR)** is the structured process an organization follows when a security incident is detected, aiming to limit damage, restore normal operations, and learn from what happened. Having a documented, practiced plan *before* an incident occurs makes an enormous difference — decisions made calmly in advance are far better than decisions made under pressure during a live crisis.

---

## 34.2 The Incident Response Lifecycle

A widely used model (based on NIST guidance) breaks IR into six phases:

1. **Preparation** — building the plan, tools, training, and team roles before anything happens.
2. **Identification** — determining that a security incident has actually occurred, distinguishing it from a false alarm.
3. **Containment** — limiting the incident's spread, often split into short-term containment (immediate stopgap, e.g., isolating an infected machine) and long-term containment (a more durable fix while investigation continues).
4. **Eradication** — removing the root cause, such as malware, a malicious account, or the vulnerability that enabled access.
5. **Recovery** — restoring affected systems to normal operation, carefully verifying they're clean before reconnecting them.
6. **Lessons Learned** — a post-incident review to identify what worked, what didn't, and what should change going forward.

---

## 34.3 The Incident Response Team

Effective response usually involves more than just technical staff:

- **Incident responders/analysts** — the technical team investigating and containing the issue.
- **Management/leadership** — makes business-impact decisions, such as whether to shut down a revenue-generating system.
- **Legal counsel** — advises on regulatory notification obligations and liability.
- **Communications/PR** — manages internal and external messaging.
- **Third parties** — such as external forensics firms, law enforcement, or cyber insurance providers, depending on the incident's severity.

---

## 34.4 Communication During an Incident

Clear communication channels matter enormously during a live incident — including having a plan for communicating *if* normal channels (like corporate email) might themselves be compromised. Many organizations maintain a pre-defined, tested "break glass" communication method for exactly this scenario.

---

## 34.5 Why Preparation Matters Most

Organizations that regularly conduct **tabletop exercises** (structured walkthroughs of hypothetical incident scenarios) tend to respond far more effectively when a real incident occurs, because roles, decision points, and escalation paths have already been rehearsed rather than improvised. Incident response plans should be treated as living documents, updated after every real incident and periodically reviewed even without one.

[Previous](./[33]-Intrusion-Detection-and-Prevention-Systems.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[35]-Digital-Forensics-Basics.md)
