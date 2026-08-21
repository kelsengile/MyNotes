[Previous](./[4]-Regions-Availability-Zones-and-Global-Infrastructure.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[6]-Cloud-Networking-Basics.md)

*Core Concepts*

# Lesson 5 - IAM: Users, Roles & Permissions

## 5.1 Users and Groups

**Identity and Access Management (IAM)** controls who can do what in your cloud account. A **user** represents a single person or application and has its own credentials (password for console access, access keys for CLI/API access). Rather than assigning permissions to each user individually, users are organized into **groups** (e.g. "Developers", "Billing-Admins"), and permissions are attached to the group — every member inherits them. This keeps permission management consistent as teams grow.

---

## 5.2 Roles and Policies

A **policy** is a document (usually JSON) that defines what actions are allowed or denied on which resources. Example AWS policy snippet:

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

A **role** is similar to a user but isn't tied to one person — it's assumed temporarily by a user, service, or application to gain a specific set of permissions for a limited time. Roles are the standard way to grant an EC2 instance, Lambda function, or CI/CD pipeline access to other resources without embedding long-lived credentials in code.

---

## 5.3 Principle of Least Privilege

The **principle of least privilege** means granting only the minimum permissions needed to perform a task — nothing more. In practice this means avoiding broad wildcard permissions (`"Action": "*"`, `"Resource": "*"`), scoping policies to specific resources and actions, and regularly auditing unused permissions. Least privilege limits the damage if credentials are ever leaked or an application is compromised, since an attacker can only do what that identity was explicitly allowed to do. Combined with MFA and short-lived credentials, it forms the core of cloud identity security.

---

[Previous](./[4]-Regions-Availability-Zones-and-Global-Infrastructure.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[6]-Cloud-Networking-Basics.md)
