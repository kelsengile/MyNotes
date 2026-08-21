[Previous](./[36]-Authentication-Methods.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[38]-Zero-Trust-Architecture-Basics.md)

*Identity & Access Management*

# Lesson 37 - Identity & Access Management Concepts

## 37.1 What is IAM?

**Identity and Access Management (IAM)** is the discipline of managing digital identities and controlling what those identities are permitted to access, throughout their entire lifecycle. It ties together concepts introduced earlier — authentication (Lesson 36), authorization, and access control models (Lesson 13) — into an organization-wide practice.

---

## 37.2 The Identity Lifecycle

IAM manages an identity through several stages:

- **Provisioning** — creating an account and granting appropriate initial access when someone joins an organization or takes on a new role.
- **Access reviews** — periodically verifying that existing access is still appropriate, catching cases where access should have been revoked but wasn't.
- **Modification** — adjusting access as a person's role changes (a common source of "access creep," discussed in Lesson 13, if old access isn't removed).
- **Deprovisioning** — promptly revoking all access when someone leaves the organization or no longer needs it; delayed deprovisioning is a common, serious real-world security gap.

---

## 37.3 Privileged Access Management (PAM)

**Privileged accounts** — those with elevated access, like domain administrators or database superusers — represent outsized risk if compromised, since they typically grant broad access across many systems. **PAM** solutions add extra controls specifically for these accounts:

- **Just-in-time access** — granting elevated privileges only for a limited time window when actually needed, rather than leaving them permanently active.
- **Privileged session monitoring/recording** — logging exactly what privileged users do during elevated sessions.
- **Credential vaulting** — storing privileged credentials in a secure, access-controlled vault rather than letting them be remembered or shared informally.

---

## 37.4 The Principle of Least Privilege, Revisited

IAM operationalizes the least-privilege principle introduced in Lesson 13 at an organizational scale: every account should have the minimum access necessary for its role, for the minimum time necessary, reviewed regularly. This significantly limits the blast radius of any single compromised account — an attacker who steals a low-privilege account's credentials can only do limited damage, compared to a broadly over-permissioned one.

---

## 37.5 Federated Identity

**Federated identity** extends IAM concepts across organizational boundaries — for example, allowing a partner company's employees to access certain shared systems using their own company's credentials, rather than creating and managing separate accounts for them. This relies on the same trust relationships and protocols (like SAML and OpenID Connect) that power SSO within a single organization (Lesson 36).

[Previous](./[36]-Authentication-Methods.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[38]-Zero-Trust-Architecture-Basics.md)
