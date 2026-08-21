[Previous](./[32]-Alerting-and-Incident-Response.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[34]-Encryption-at-Rest-and-in-Transit.md)

*Security*

# Lesson 33 - Cloud Security Fundamentals

## 33.1 Shared Responsibility Model

Cloud security follows the **shared responsibility model**: the provider secures the infrastructure "of" the cloud (physical data centers, host hardware, the virtualization layer, and for managed services, the underlying platform), while you're responsible for security "in" the cloud (your data, your IAM configuration, your network settings, your application code, and how you configure the managed services you use). A misconfigured S3 bucket or overly permissive IAM policy is a *customer* responsibility failure, not a provider failure — this model is expanded on fully in Lesson 36.

---

## 33.2 Common Threats

Cloud environments face threats including:

- **Misconfiguration** — the single most common cause of cloud breaches: publicly exposed storage buckets, overly broad IAM permissions, open security group rules.
- **Compromised credentials** — leaked access keys (e.g. accidentally committed to a public repository) giving an attacker direct account access.
- **Insecure APIs** — poorly authenticated or validated application endpoints.
- **Insider threats** — misuse of legitimate access by an employee or contractor.
- **DDoS attacks** — overwhelming a service with traffic to disrupt availability.

---

## 33.3 Security Best Practices

Foundational practices to defend against these threats:

- Apply the **principle of least privilege** (Lesson 5) to every user, role, and service.
- Enable **multi-factor authentication (MFA)** everywhere, especially on privileged accounts.
- **Never commit secrets** (keys, passwords, tokens) to source control — use a secrets manager instead (Lesson 35).
- **Encrypt data** at rest and in transit by default (Lesson 34).
- **Enable logging** (e.g. AWS CloudTrail) to record every API call made in your account, so unusual activity can be detected and investigated.
- **Regularly audit** IAM permissions, security group rules, and public-facing resources for unintended exposure.
- **Patch and update** systems promptly, especially for known vulnerabilities.

---

[Previous](./[32]-Alerting-and-Incident-Response.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[34]-Encryption-at-Rest-and-in-Transit.md)
