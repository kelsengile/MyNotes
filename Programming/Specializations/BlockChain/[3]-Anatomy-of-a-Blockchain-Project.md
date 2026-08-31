[Previous](./%5B2%5D-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./%5B4%5D-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 3 - Anatomy of a Blockchain Project

## 3.1 Typical Folder Structure

A standard Hardhat or Foundry project is organized so that contracts, tests, and deployment logic are clearly separated:

- `contracts/` (or `src/`) — Solidity source files.
- `test/` — Automated tests written in JavaScript/TypeScript (Hardhat) or Solidity (Foundry).
- `scripts/` (or `script/`) — Deployment and interaction scripts.
- `artifacts/` or `out/` — Compiler output (ABI and bytecode), auto-generated and not committed to version control.
- `hardhat.config.js` / `foundry.toml` — Project configuration, including network settings and compiler version.

---

## 3.2 The Compilation Pipeline

Solidity source code is compiled into two key artifacts: **bytecode**, the low-level instructions the Ethereum Virtual Machine actually executes, and the **ABI** (Application Binary Interface), a JSON description of a contract's functions and events that other programs use to know how to interact with it. Every time a contract's source changes, it must be recompiled before it can be deployed or tested against.

---

## 3.3 Front-End and Off-Chain Components

Many blockchain projects are more than just contracts. A typical dApp also includes:

- A **front-end** (often React or similar) that lets users interact with the contract through their wallet.
- An **off-chain layer**, such as a server, indexer, or subgraph, that stores or queries data too large or expensive to keep entirely on-chain.
- **Configuration for multiple networks** (mainnet, testnets, local nodes), since the same contract code is usually deployed and tested across several environments before going live.

---

## 3.4 Dependencies and Libraries

Blockchain projects commonly depend on audited, community-maintained libraries rather than writing everything from scratch — most notably OpenZeppelin's contract library, which provides secure, standard implementations of tokens (ERC-20, ERC-721), access control, and upgradeability patterns. Pulling in audited code for common patterns significantly reduces the risk of introducing security bugs.

[Previous](./%5B2%5D-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./%5B4%5D-Variables-and-Data-Typesmd)
