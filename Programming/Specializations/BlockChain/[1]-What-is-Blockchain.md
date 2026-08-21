[Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[2]-Development-Environment.md)

*Getting Started*

# Lesson 1 - What is Blockchain? Core Concepts & History

## 1.1 What is a Blockchain?

A blockchain is a distributed, append-only ledger made up of blocks of data that are cryptographically linked together in chronological order. Instead of a single company or server owning the ledger, copies of it are held by many independent computers, called nodes, that all agree on its contents through a process called consensus. Once a block is added and confirmed, changing it would require rewriting every block after it on the majority of nodes at once, which makes the history extremely difficult to tamper with.

---

## 1.2 Decentralization vs. Centralization

Traditional systems (a bank's database, a company's server) are centralized: one party controls the data and can change or restrict it. Blockchains are decentralized: no single party controls the ledger, and any participant can verify the full history for themselves. This trade-off buys censorship-resistance and trust-minimization at the cost of speed, storage duplication, and coordination overhead compared to a centralized database.

---

## 1.3 A Brief History

- **2008** — An individual or group using the name Satoshi Nakamoto published the Bitcoin whitepaper, describing a peer-to-peer electronic cash system that solved the "double-spend" problem without a trusted third party.
- **2009** — The Bitcoin network launched, introducing Proof of Work mining and the first working blockchain.
- **2015** — Ethereum launched, adding a built-in programming language (Solidity) and a virtual machine (the EVM), allowing developers to write general-purpose "smart contracts" that run on-chain, not just transfer currency.
- **2017–2021** — The token boom (ICOs), the rise of decentralized finance (DeFi), and non-fungible tokens (NFTs) demonstrated that blockchains could host far more than payments.
- **2022–present** — The ecosystem has shifted toward scaling (Layer 2 networks), Ethereum's move to Proof of Stake, and increasing regulatory attention.

---

## 1.4 Core Properties

- **Immutability** — Confirmed data is extremely costly to alter.
- **Transparency** — Most public blockchains let anyone inspect every transaction ever recorded.
- **Trust minimization** — Participants don't need to trust each other individually, only the protocol's rules.
- **Programmability** — Modern blockchains (like Ethereum) let developers deploy self-executing code (smart contracts) directly on the ledger.

These four properties are the foundation for everything covered in the rest of this course — from writing smart contracts to building decentralized applications.

 [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[2]-Development-Environment.md)
