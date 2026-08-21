[Previous](./[10]-Cryptographic-Hashing-and-Digital-Signatures.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[12]-Transactions-and-Gas.md)

*Blockchain Fundamentals*

# Lesson 11 - Wallets, Keys & Addresses

## 11.1 From Private Key to Address

An Ethereum address is derived deterministically: a private key generates a public key through elliptic-curve multiplication, and the address is the last 20 bytes of the Keccak-256 hash of that public key. This means an address can always be recomputed from its key pair, but there is no way to work backward from an address to a private key.

---

## 11.2 Seed Phrases and HD Wallets

Rather than managing many separate private keys, most modern wallets are "hierarchical deterministic" (HD) wallets: a single 12- or 24-word **seed phrase** (following the BIP-39 standard) can deterministically generate an entire tree of key pairs. Backing up the seed phrase backs up every account derived from it, which is why losing or exposing a seed phrase is equivalent to losing or exposing every account it controls.

---

## 11.3 Wallet Types

- **Hot wallets** — connected to the internet (browser extensions like MetaMask, mobile apps). Convenient for everyday use, but more exposed to malware and phishing.
- **Cold wallets** — private keys are generated and stored offline (hardware wallets like Ledger or Trezor, or even paper). Far more resistant to remote attacks, at the cost of convenience.
- **Custodial wallets** — a third party (like an exchange) holds the private keys on the user's behalf. Convenient, but the user must trust the custodian, giving rise to the phrase "not your keys, not your coins."

---

## 11.4 Externally Owned Accounts vs. Contract Accounts

Ethereum has two account types: **Externally Owned Accounts (EOAs)**, controlled by a private key and able to initiate transactions, and **Contract Accounts**, controlled by their own code and only able to act in response to a transaction or message sent to them. Both share the same address format, but only EOAs can be the original sender of a transaction.

[Previous](./[10]-Cryptographic-Hashing-and-Digital-Signatures.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[12]-Transactions-and-Gas.md)
