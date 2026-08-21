[Previous](./[38]-Zero-Trust-Architecture-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[40]-Secure-Software-Development-Lifecycle.md)

*Cloud & Application Security*

# Lesson 39 - Cloud Security Fundamentals

## 39.1 Cloud Service Models

Cloud computing is generally divided into three service models, each shifting different security responsibilities to the provider:

- **IaaS (Infrastructure as a Service)** — the provider manages physical hardware and virtualization; the customer manages the OS, applications, and data (e.g., AWS EC2, Azure VMs).
- **PaaS (Platform as a Service)** — the provider also manages the OS and runtime; the customer manages only their application and data (e.g., AWS Elastic Beanstalk, Azure App Service).
- **SaaS (Software as a Service)** — the provider manages the entire application; the customer manages only their data and user access (e.g., Microsoft 365, Salesforce).

---

## 39.2 The Shared Responsibility Model

Cloud security operates on a **shared responsibility model**: the provider is responsible for the security *of* the cloud (physical infrastructure, host virtualization), while the customer is responsible for security *in* the cloud (data, access configuration, and — depending on the service model — the operating system and application). A huge number of real-world cloud breaches stem from customers misunderstanding this split and assuming the provider handles something that was actually their own responsibility.

---

## 39.3 Common Cloud Misconfigurations

Misconfiguration, rather than a flaw in the cloud provider's own infrastructure, is the leading cause of cloud security incidents. Common examples include:

- **Publicly exposed storage buckets** (e.g., an AWS S3 bucket set to public when it should be private), accidentally exposing sensitive data to the entire internet.
- **Overly permissive IAM policies** — granting broad permissions (like full administrative access) when a narrowly scoped role would suffice, violating least privilege.
- **Unencrypted data at rest or in transit** — failing to enable encryption options the provider makes available.
- **Exposed management interfaces** — leaving administrative consoles or APIs reachable from the public internet without adequate access restrictions.

---

## 39.4 Cloud-Specific Security Tools and Concepts

- **CSPM (Cloud Security Posture Management)** — tools that continuously scan cloud environments for misconfigurations against security best practices.
- **CASB (Cloud Access Security Broker)** — sits between users and cloud services to enforce security policy and visibility, especially relevant for SaaS usage.
- **Cloud IAM** — cloud providers offer their own fine-grained access control systems (e.g., AWS IAM), which apply the least-privilege and role-based concepts from Lesson 13 at cloud scale.

---

## 39.5 Multi-Tenancy and Isolation

Public cloud infrastructure is inherently **multi-tenant** — many customers share the same underlying physical hardware, logically isolated from one another by the provider's virtualization and access controls. Understanding this shared infrastructure model helps explain why strong identity and configuration controls (rather than physical isolation alone) are central to cloud security.

[Previous](./[38]-Zero-Trust-Architecture-Basics.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[40]-Secure-Software-Development-Lifecycle.md)
