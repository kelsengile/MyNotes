[Previous](./[9]-Consensus-Mechanisms.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[11]-Wallets-Keys-and-Addresses.md)

*Blockchain Fundamentals*

# Lesson 10 - Cryptographic Hashing & Digital Signatures

## 10.1 Hash Functions

A cryptographic hash function takes input of any size and produces a fixed-size output (a "digest") with three key properties: the same input always produces the same output (deterministic), it's computationally infeasible to reverse the output back to the input (one-way), and even a tiny change to the input produces a completely different output (the avalanche effect). Ethereum uses **Keccak-256**; Bitcoin uses **SHA-256**. Hashes are used throughout blockchains — linking blocks, building Merkle trees, and deriving addresses.

---

## 10.2 Public-Key Cryptography

Blockchains rely on asymmetric (public-key) cryptography, where each account has a mathematically linked pair of keys: a **private key**, kept secret, and a **public key**, which can be shared freely. Data signed with a private key can be verified by anyone using the corresponding public key, without ever revealing the private key itself. Ethereum uses the ECDSA algorithm over the secp256k1 curve.

---

## 10.3 Digital Signatures

To send a transaction, a wallet signs the transaction data with its private key. Anyone on the network can then use the sender's public key to verify that the signature is valid — proving the transaction genuinely came from the key holder and wasn't altered in transit — without the sender ever exposing their private key. This is what allows the network to authenticate transactions without a central authority checking IDs.

---

## 10.4 Why This Matters for Security

Because control of an account is entirely determined by control of its private key, losing a private key means permanently losing access to the account, and anyone who obtains it gains full control. This is why secure key storage (covered next in Lesson 11) and never sharing a private key or seed phrase are foundational rules in blockchain development and usage.

[Previous](./[9]-Consensus-Mechanisms.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[11]-Wallets-Keys-and-Addresses.md)
