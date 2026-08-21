[Previous](./[16]-Hashing-and-Digital-Signatures.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[18]-Common-Cryptographic-Pitfalls.md)

*Cryptography*

# Lesson 17 - TLS/SSL & Certificates

## 17.1 From SSL to TLS

**TLS (Transport Layer Security)** is the protocol that secures data in transit across the internet — it's the "S" in HTTPS. TLS is the modern successor to **SSL (Secure Sockets Layer)**; SSL is now considered obsolete and insecure, but the name "SSL" still sticks around colloquially even when people mean TLS.

TLS provides confidentiality (encryption), integrity (tamper detection), and authentication (verifying you're talking to the real server, not an impostor) — directly applying the concepts from Lessons 15 and 16.

---

## 17.2 The TLS Handshake

Before any application data is exchanged, the client and server perform a **handshake**:

1. The client and server agree on a TLS version and a set of cryptographic algorithms ("cipher suite") to use.
2. The server presents its **digital certificate** to prove its identity.
3. The client and server use asymmetric cryptography to securely agree on a shared **symmetric session key**.
4. From that point on, all traffic is encrypted using the fast symmetric key — this hybrid approach mirrors the pattern discussed in Lesson 15.

---

## 17.3 Digital Certificates and the Chain of Trust

A **digital certificate** binds a public key to an identity (like a domain name), and is itself digitally signed by a trusted **Certificate Authority (CA)**. Your browser trusts a certificate because it trusts the CA that signed it — this is the **chain of trust**:

`Root CA → Intermediate CA → Your Website's Certificate`

Browsers and operating systems ship with a built-in list of trusted root CAs. If a certificate is self-signed, expired, or issued for the wrong domain, browsers will display a warning, because the chain of trust can't be verified.

---

## 17.4 Why Certificate Validation Matters

Certificate validation is what prevents a classic **Man-in-the-Middle (MitM)** attack: without it, an attacker intercepting your connection could present their own certificate, decrypt your traffic, and pass it along to the real server without you noticing. This is why security professionals strongly advise against clicking through browser certificate warnings, and why organizations sometimes deliberately break this trust internally (with employee consent) for traffic inspection, using their own trusted internal CA.

---

## 17.5 Practical Considerations

- **HSTS (HTTP Strict Transport Security)** — a header that tells browsers to always use HTTPS for a site, preventing downgrade attacks to plain HTTP.
- **Certificate expiration** — certificates have a limited validity period and must be renewed; an expired certificate breaks trust even if nothing else changed.
- **Free automated certificates** — services like Let's Encrypt have made obtaining and renewing valid TLS certificates free and largely automated, removing cost as a barrier to widespread HTTPS adoption.

[Previous](./[16]-Hashing-and-Digital-Signatures.md) | [Table of Contents](./[0]-Introduction-to-Cybersecurity.md) | [Next](./[18]-Common-Cryptographic-Pitfalls.md)
