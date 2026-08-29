[Previous](./[33]-Cloud-Security-Fundamentals.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[35]-Secrets-Management.md)

*Security*

# Lesson 34 - Encryption at Rest & in Transit

## 34.1 Encryption at Rest

**Encryption at rest** protects stored data — on disk volumes, in databases, in object storage — so that even if the underlying physical storage were somehow accessed directly, the data would be unreadable without the correct decryption key. Most managed cloud services (S3, RDS, EBS, etc.) support encryption at rest as a simple configuration toggle, often enabled by default today, using strong standard algorithms (typically AES-256) so developers don't need to implement encryption logic themselves.

---

## 34.2 Encryption in Transit

**Encryption in transit** protects data as it moves across a network — between a user's browser and your servers, or between two internal services — primarily via **TLS (Transport Layer Security)**, the protocol behind HTTPS. Without it, data traveling across a network could be intercepted and read (or tampered with) by anyone positioned between the two endpoints. Best practice is enforcing TLS everywhere: not just for public-facing traffic, but for internal service-to-service communication too, since internal networks shouldn't be assumed to be inherently trustworthy.

---

## 34.3 Key Management

Encryption is only as strong as the protection of its **keys**. Cloud providers offer managed key management services — AWS KMS, Azure Key Vault, Google Cloud KMS — that generate, store, rotate, and control access to encryption keys, without ever exposing the raw key material to you directly. These services integrate with other services so encryption is enabled with a simple reference to a key ID, and every use of a key can be logged and audited. Two common patterns:

- **Provider-managed keys** — the cloud provider fully manages the key, simplest to use.
- **Customer-managed keys (CMK)** — you control the key's policy, rotation schedule, and who can use it, offering more control at the cost of more operational responsibility.

Regular **key rotation** (periodically replacing keys) limits the impact if a key is ever compromised.

---

[Previous](./[33]-Cloud-Security-Fundamentals.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[35]-Secrets-Management.md)
