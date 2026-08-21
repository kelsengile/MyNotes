[Previous](./[17]-TLS-SSL-and-Certificates.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[19]-OWASP-Top-10-Overview.md)

*Cryptography*

# Lesson 18 - Common Cryptographic Pitfalls

## 18.1 Using Broken or Outdated Algorithms

Cryptographic algorithms don't stay secure forever — as computing power grows and researchers find weaknesses, once-strong algorithms become breakable. Common mistakes include still using **MD5** or **SHA-1** for security purposes (both have demonstrated collision vulnerabilities), or using outdated protocols like **SSLv3** or early TLS versions with known weaknesses. Staying current with cryptographic guidance (e.g., from NIST) is an ongoing responsibility, not a one-time decision.

---

## 18.2 Weak Key Management

Even a strong algorithm fails if the key itself is mishandled:

- **Hardcoding keys or secrets** directly in source code, where they can be found by anyone with access to the repository (a very common real-world finding in security audits).
- **Reusing keys** across multiple systems or purposes, so that a single compromised key has an outsized impact.
- **Weak key generation** — using predictable or low-entropy sources to generate keys, making them easier to guess or brute-force.
- **Poor key storage** — storing private keys without adequate protection, instead of using dedicated secret managers or Hardware Security Modules (HSMs).

---

## 18.3 Misusing Hashing

- **Using fast, general-purpose hashes (like SHA-256) for password storage** instead of dedicated slow algorithms like bcrypt or Argon2 (Lesson 16), making brute-force attacks much easier.
- **Skipping salts**, allowing attackers to use precomputed rainbow tables to crack many passwords at once.
- **Confusing hashing with encryption** — hashing is one-way and can't be "decrypted"; using it where reversible encryption is actually needed is a design error.

---

## 18.4 Implementation Mistakes

- **Rolling your own crypto** — implementing custom encryption algorithms instead of using established, peer-reviewed libraries. Even correct-looking custom implementations often contain subtle flaws that only surface under expert cryptanalysis.
- **Predictable Initialization Vectors (IVs) or nonces** — many encryption modes require a unique, unpredictable value for each encryption operation; reusing or predicting it can catastrophically weaken the encryption.
- **Padding oracle vulnerabilities** — flawed error handling around encrypted data padding that can let an attacker gradually decrypt data through repeated queries.
- **Ignoring certificate validation errors** in code (e.g., disabling TLS certificate checks "temporarily" during development, and forgetting to re-enable them).

---

## 18.5 Practical Takeaways

- Use well-established, actively maintained cryptographic libraries — never implement your own primitives.
- Follow current guidance from trusted bodies (like NIST) on approved algorithms and key sizes.
- Use dedicated secret management tools rather than hardcoding or informally storing keys.
- Plan for **cryptographic agility** — the ability to swap out algorithms as they age, without a full system redesign.

[Previous](./[17]-TLS-SSL-and-Certificates.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[19]-OWASP-Top-10-Overview.md)
