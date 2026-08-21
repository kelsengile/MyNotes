[Previous](./[31]-Privilege-Escalation-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[33]-Intrusion-Detection-and-Prevention-Systems.md)

*Defensive Security*

# Lesson 32 - Security Monitoring & SIEM Basics

## 32.1 Why Monitoring Matters

Prevention controls (firewalls, patching, access control) reduce risk but can never eliminate it entirely — some attacks will get through. **Security monitoring** is how defenders detect that something has gone wrong, ideally quickly enough to respond before serious damage occurs. Without monitoring, an intrusion can go unnoticed for weeks or months.

---

## 32.2 Logs: The Foundation of Monitoring

A **log** is a recorded event: a login attempt, a file access, a firewall rule triggering, an application error. Individually, logs are just data; analyzed together, they reveal patterns that indicate normal activity versus an attack. Common log sources include operating systems (Lessons 11–12), firewalls, applications, and cloud services.

Effective logging requires deciding what to log (too little misses attacks; too much creates unmanageable noise), for how long to retain logs (balancing storage cost against investigative need), and ensuring logs themselves are protected from tampering by an attacker trying to cover their tracks.

---

## 32.3 What is a SIEM?

A **SIEM (Security Information and Event Management)** system centralizes log collection from across an organization's entire environment, correlating events from multiple sources to detect patterns a single log source alone wouldn't reveal. For example, a SIEM might correlate a failed login on a VPN with a successful login moments later from an unusual location — individually unremarkable, but suspicious together.

Key SIEM capabilities:

- **Log aggregation** — collecting logs from many systems into one place.
- **Correlation rules** — defining patterns across events that indicate suspicious activity.
- **Alerting** — notifying analysts when a correlation rule triggers.
- **Dashboards and reporting** — visualizing security posture and trends over time.

---

## 32.4 The Security Operations Center (SOC)

A **SOC (Security Operations Center)** is the team (and often physical/virtual space) responsible for continuously monitoring an organization's security posture, typically using a SIEM as a core tool. SOC analysts triage alerts, investigate suspicious activity, and escalate confirmed incidents to the incident response process (Lesson 34).

SOCs are often organized in tiers: **Tier 1** analysts perform initial triage of alerts, **Tier 2** analysts investigate more deeply, and **Tier 3** analysts (or threat hunters) handle the most complex incidents and proactively search for threats that automated tools might miss.

---

## 32.5 Alert Fatigue

A common real-world challenge is **alert fatigue** — when a SIEM generates so many alerts (many of them false positives) that analysts become desensitized and may miss genuine threats buried in the noise. Tuning detection rules to reduce false positives, and prioritizing alerts by severity, is an ongoing and essential part of running an effective monitoring program.

[Previous](./[31]-Privilege-Escalation-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[33]-Intrusion-Detection-and-Prevention-Systems.md)
