[Previous](./[3]-Legal-and-Ethical-Considerations.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[5]-Threats-Vulnerabilities-and-Risk.md)

*Core Concepts*

# Lesson 4 - The CIA Triad & Security Principles

## 4.1 Confidentiality

**Confidentiality** means ensuring information is only accessible to those authorized to see it. It's protected through mechanisms such as encryption, access controls, and authentication.

A confidentiality failure is a **data breach** — for example, an attacker reading sensitive customer records they shouldn't have access to. Confidentiality controls answer the question: "Who is allowed to see this?"

---

## 4.2 Integrity

**Integrity** means ensuring information is accurate and hasn't been tampered with, whether by an attacker, a system error, or an unauthorized change. It's protected through mechanisms such as hashing, digital signatures, and version control.

An integrity failure might look like an attacker silently modifying a bank transaction amount, or altering log files to hide evidence of an intrusion. Integrity controls answer the question: "Can I trust that this hasn't been changed?"

---

## 4.3 Availability

**Availability** means ensuring systems and data are accessible to authorized users when needed. It's protected through redundancy, backups, and capacity planning, and threatened by things like Denial-of-Service (DoS) attacks or hardware failure.

An availability failure might be a hospital's patient record system going offline during a ransomware attack, preventing doctors from accessing critical information. Availability controls answer the question: "Can authorized users get to this when they need to?"

---

## 4.4 Balancing the Triad

The three principles of the CIA triad often trade off against each other. Extremely strict confidentiality controls (like requiring multiple approvals to access any file) can hurt availability by slowing down legitimate users. Extremely open availability (like giving everyone access to speed things up) can hurt confidentiality. Good security design finds the right balance for each system based on its actual risk.

---

## 4.5 Supporting Principles: AAA and Non-Repudiation

Beyond the CIA triad, a few closely related principles come up constantly:

- **Authentication** — verifying that someone is who they claim to be (e.g., a password or fingerprint).
- **Authorization** — determining what an authenticated user is allowed to do.
- **Accounting (or Auditing)** — recording what actions were taken, and by whom, typically via logs.
- **Non-repudiation** — ensuring someone can't credibly deny having performed an action (often achieved with digital signatures, covered in Lesson 16).

Together, these principles form the foundation that almost every other topic in this course builds on.

[Previous](./[3]-Legal-and-Ethical-Considerations.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[5]-Threats-Vulnerabilities-and-Risk.md)
