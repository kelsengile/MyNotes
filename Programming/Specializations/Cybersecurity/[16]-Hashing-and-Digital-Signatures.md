[Previous](./[15]-Cryptography-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[17]-TLS-SSL-and-Certificates.md)

*Cryptography*

# Lesson 16 - Hashing & Digital Signatures

## 16.1 What is Hashing?

A **hash function** takes an input of any size and produces a fixed-size output (a "hash" or "digest"). Unlike encryption, hashing is a **one-way** process — there's no key to reverse it back into the original input. Good cryptographic hash functions have three key properties:

- **Deterministic** — the same input always produces the same output.
- **Avalanche effect** — a tiny change in input produces a completely different output.
- **Collision-resistant** — it should be computationally infeasible to find two different inputs that produce the same hash.

Common algorithms include **SHA-256** (widely used and currently considered secure) and **MD5**/**SHA-1** (both now considered broken for security purposes due to demonstrated collision vulnerabilities, though still sometimes seen in legacy systems).

---

## 16.2 Common Uses of Hashing

- **Password storage** — systems store a hash of a password, not the password itself, so that even if the database is stolen, the actual passwords aren't directly exposed. Because attackers can pre-compute hashes for common passwords ("rainbow tables"), a random **salt** is added to each password before hashing to make each hash unique, even for identical passwords.
- **Integrity verification** — comparing the hash of a downloaded file against a published hash confirms the file wasn't corrupted or tampered with in transit.
- **Digital forensics** — investigators hash evidence (like a disk image) at the start of an investigation, so they can later prove it wasn't altered.

---

## 16.3 Password Hashing Algorithms

General-purpose hash functions like SHA-256 are actually *too fast* for password storage — their speed makes brute-force guessing easier. Dedicated password-hashing algorithms are deliberately slow and resource-intensive to resist brute-force attacks:

- **bcrypt**, **scrypt**, and **Argon2** (currently considered the strongest, and the winner of the Password Hashing Competition) are the recommended choices for storing passwords.

---

## 16.4 Digital Signatures

A **digital signature** uses asymmetric cryptography (Lesson 15) to prove that a message came from a specific sender and wasn't altered. The process, in simplified form:

1. The sender hashes the message.
2. The sender encrypts that hash with their **private key** — this encrypted hash is the signature.
3. Anyone with the sender's **public key** can decrypt the signature to recover the original hash, then independently hash the message themselves and compare the two.

If the hashes match, it proves both **integrity** (the message wasn't altered) and **authenticity/non-repudiation** (only the holder of the private key could have created that signature). Digital signatures underpin software update verification, signed emails, and — critically — the certificates that make HTTPS trustworthy (Lesson 17).

[Previous](./[15]-Cryptography-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[17]-TLS-SSL-and-Certificates.md)
