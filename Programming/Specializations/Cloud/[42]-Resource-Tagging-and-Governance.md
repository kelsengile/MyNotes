[Previous](./[41]-Cost-Optimization-Strategies.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[43]-Multi-Cloud-and-Hybrid-Cloud-Strategies.md)

*Cost & Optimization*

# Lesson 42 - Resource Tagging & Governance

## 42.1 Why Tagging Matters

A **tag** is a key-value label attached to a cloud resource (e.g. `Environment: production`, `Team: payments`, `Project: checkout-redesign`). At small scale, resource ownership is obvious; at real organizational scale with hundreds or thousands of resources across many teams, tags are what makes it possible to answer basic questions like "who owns this," "what project is this for," and "how much does each team actually cost us" — none of which are answerable from resource names alone.

---

## 42.2 Tagging Strategies

An effective tagging strategy typically defines a small set of **required** tags applied consistently to every resource, such as:

- `Environment` — e.g. `dev`, `staging`, `production`.
- `Owner`/`Team` — who is responsible for this resource.
- `Project`/`CostCenter` — for cost attribution and billing (Lesson 40).
- `ManagedBy` — e.g. `terraform`, to distinguish IaC-managed resources from manually created ones.

Consistency matters more than an exhaustive tag list — a small set of tags applied to *everything* is far more useful than a large set applied inconsistently. Tags are also commonly used in IaC (Lesson 20) to automatically tag every resource a configuration creates, ensuring nothing slips through untagged.

---

## 42.3 Governance Tools

Beyond manual discipline, providers offer governance tools to enforce standards automatically:

- **Tag policies** — reject or flag resources created without required tags.
- **AWS Organizations / Azure Management Groups / GCP Resource Manager** — hierarchical structures for managing multiple accounts/subscriptions/projects under centralized policy control.
- **Service Control Policies (SCPs)** and similar guardrails — restrict which actions are allowed account-wide, regardless of individual IAM permissions (e.g. blocking resource creation in unapproved regions).
- **AWS Config / Azure Policy / GCP Organization Policy** — continuously monitor resources for compliance with defined rules and can auto-remediate violations.

These governance tools scale the discipline of good practices (least privilege, tagging, budget limits) across an entire organization without relying on every individual engineer remembering to follow them manually.

---

[Previous](./[41]-Cost-Optimization-Strategies.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[43]-Multi-Cloud-and-Hybrid-Cloud-Strategies.md)
