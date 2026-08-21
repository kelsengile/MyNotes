[Previous](./[37]-Identity-and-Access-Management-Concepts.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[39]-Cloud-Security-Fundamentals.md)

*Identity & Access Management*

# Lesson 38 - Zero Trust Architecture Basics

## 38.1 The Traditional "Castle and Moat" Model

Traditional network security often followed a **perimeter-based** model: strong defenses at the network edge (firewalls, VPNs), but relatively high implicit trust for anything already inside the network. The problem with this model is that once an attacker breaches the perimeter — or an insider threat already has legitimate internal access — they often face minimal additional resistance, allowing easy lateral movement (Lesson 31).

---

## 38.2 The Zero Trust Principle

**Zero Trust** is built on a simple guiding principle: **"never trust, always verify."** No user, device, or system is automatically trusted just because it's on the internal network — every access request is verified based on identity, device health, and context, regardless of where the request originates.

This doesn't mean literally trusting nothing ever; it means removing the assumption that "inside the network" equals "safe," and instead continuously verifying every request against policy.

---

## 38.3 Core Components of Zero Trust

- **Strong identity verification** — every request requires strong authentication (Lesson 36), not just network location.
- **Least privilege access** — users and devices get only the minimum access needed for a specific task, consistent with Lesson 37.
- **Micro-segmentation** — dividing the network into small, isolated zones (Lesson 9) so that even authenticated access is limited to only what's necessary.
- **Continuous verification** — access decisions consider ongoing context (device health, unusual behavior, location) rather than a single one-time login check.
- **Assume breach** — designing systems as though an attacker may already be present, minimizing what any single compromised component can reach.

---

## 38.4 Zero Trust in Practice

Implementing Zero Trust is a gradual architectural shift, not a single product purchase, though it often involves:

- Replacing broad VPN access with application-specific, identity-aware access controls.
- Enforcing device compliance checks (e.g., requiring up-to-date patches) before granting access.
- Applying consistent policy across on-premises, cloud, and remote work environments alike.

---

## 38.5 Why Zero Trust Has Gained Traction

The rise of cloud computing (Lesson 39), remote work, and increasingly sophisticated attackers capable of breaching perimeters has made the traditional trusted-internal-network assumption increasingly risky. Zero Trust directly addresses this by ensuring that even a successfully compromised account or device faces meaningful additional resistance at every subsequent step, rather than free rein once "inside."

[Previous](./[37]-Identity-and-Access-Management-Concepts.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[39]-Cloud-Security-Fundamentals.md)
