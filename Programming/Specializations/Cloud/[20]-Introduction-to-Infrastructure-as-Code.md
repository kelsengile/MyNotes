[Previous](./[19]-API-Gateways.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[21]-Terraform-Basics.md)

*Infrastructure as Code*

# Lesson 20 - Introduction to Infrastructure as Code

## 20.1 What Is IaC?

**Infrastructure as Code (IaC)** means defining and managing your cloud infrastructure (VMs, networks, databases, permissions) through machine-readable configuration files instead of manually clicking through a web console. Those configuration files can be version-controlled, reviewed, and reused just like application code. Instead of a person remembering (or forgetting) the exact steps to set up an environment, the file itself is the single source of truth for what infrastructure should exist.

---

## 20.2 Declarative vs Imperative

IaC tools generally fall into two styles:

- **Declarative** — you describe the *desired end state* ("I want one VM, a database, and a load balancer configured like this"), and the tool figures out the steps to get there, including detecting and reconciling drift. Terraform (Lesson 21) and CloudFormation (Lesson 22) are declarative.
- **Imperative** — you write the exact *sequence of commands* to execute, step by step, similar to a script (e.g. a series of `aws` CLI commands run in order).

Declarative tools are generally preferred for infrastructure because they're idempotent — running the same configuration repeatedly produces the same result, and the tool safely calculates only the changes needed rather than blindly re-running every step.

---

## 20.3 Benefits and Tools Overview

Key benefits of IaC:

- **Repeatability** — spin up identical environments (dev, staging, production) from the same definitions.
- **Version control and review** — infrastructure changes go through the same pull-request review process as code.
- **Disaster recovery** — rebuild an entire environment from scratch quickly if needed.
- **Documentation** — the configuration itself documents exactly what infrastructure exists.

Major tools include Terraform (multi-cloud, the most widely adopted general-purpose tool), AWS CloudFormation, Azure Resource Manager (ARM) templates/Bicep, and Google Cloud Deployment Manager — the last three being cloud-native tools specific to one provider, covered in Lesson 22.

---

[Previous](./[19]-API-Gateways.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[21]-Terraform-Basics.md)
