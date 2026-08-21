[Previous](./[14]-System-Hardening.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[16]-Hashing-and-Digital-Signatures.md)

*Cryptography*

# Lesson 15 - Cryptography Fundamentals (Symmetric & Asymmetric)

## 15.1 What is Cryptography For?

**Cryptography** is the practice of securing information by transforming it so it can't be understood by unauthorized parties. In security, it primarily supports confidentiality (encryption), integrity (hashing, covered in Lesson 16), and authentication/non-repudiation (digital signatures). Understanding the basic building blocks here underpins topics like TLS (Lesson 17), password storage, and secure communication generally.

Key vocabulary: **plaintext** (the original readable data), **ciphertext** (the encrypted, unreadable form), and a **key** (the secret value that controls encryption and decryption).

---

## 15.2 Symmetric Encryption

**Symmetric encryption** uses the *same key* for both encryption and decryption. It's fast and efficient, making it well-suited for encrypting large amounts of data.

- **AES (Advanced Encryption Standard)** is the current industry-standard symmetric cipher, typically used with 128 or 256-bit keys.
- The core challenge with symmetric encryption is **key distribution** — both parties need the same secret key, but safely getting that key to both parties without it being intercepted is difficult, especially over an insecure channel.

---

## 15.3 Asymmetric Encryption

**Asymmetric (public-key) encryption** uses a mathematically linked *pair* of keys: a **public key** (freely shared) and a **private key** (kept secret). Data encrypted with the public key can only be decrypted with the corresponding private key, solving the key distribution problem — you never need to share a secret key over an insecure channel.

- **RSA** and **ECC (Elliptic Curve Cryptography)** are widely used asymmetric algorithms. ECC provides comparable security to RSA with much smaller key sizes, making it popular for mobile and embedded contexts.
- Asymmetric encryption is computationally slower than symmetric encryption, so it's typically used to securely exchange a symmetric key, which then handles the actual bulk data encryption — this hybrid approach is exactly how TLS works (Lesson 17).

---

## 15.4 Choosing the Right Approach

| | Symmetric | Asymmetric |
|---|---|---|
| Speed | Fast | Slower |
| Key management | Harder (shared secret) | Easier (public/private split) |
| Common use | Bulk data encryption | Key exchange, digital signatures |
| Example algorithms | AES | RSA, ECC |

In practice, real-world systems almost always combine both: asymmetric cryptography to safely establish a shared secret, and symmetric cryptography to efficiently encrypt the actual data.

---

## 15.5 A Word of Caution

Never design your own cryptographic algorithm or "roll your own crypto" for production use — even experienced cryptographers rely on algorithms that have been publicly reviewed and battle-tested for years. Use well-established, peer-reviewed libraries and standards, and keep them updated, since even proven algorithms can eventually be found weak or become obsolete (Lesson 18 covers common pitfalls).

[Previous](./[14]-System-Hardening.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[16]-Hashing-and-Digital-Signatures.md)
