[Previous](./[1]-What-is-Blockchain.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[3]-Anatomy-of-a-Blockchain-Project.md)

*Getting Started*

# Lesson 2 - Development Environment & Toolchains (Node, Hardhat/Foundry, Wallets)

## 2.1 Node.js and Package Managers

Most Ethereum tooling is written in JavaScript/TypeScript and distributed through npm, so a working Node.js installation (LTS version recommended) is the starting point for almost every blockchain project. Package managers like `npm`, `yarn`, or `pnpm` are used to install project dependencies such as contract frameworks, testing libraries, and front-end tooling, and to run project scripts defined in `package.json`.

---

## 2.2 Development Frameworks: Hardhat and Foundry

- **Hardhat** is a JavaScript/TypeScript-based development environment for compiling, testing, debugging, and deploying Solidity smart contracts. It includes a local Ethereum network for fast iteration and a plugin ecosystem for tasks like verification and gas reporting.
- **Foundry** is a fast, Rust-based toolchain that lets developers write both contracts and tests in Solidity itself. Its components include `forge` (build/test), `cast` (send transactions/query chain data), and `anvil` (a local test node).

Both are industry-standard; many teams use Hardhat for its JavaScript-friendly workflow and Foundry for its speed and Solidity-native testing. Later lessons in the Tooling & Testing section cover both in depth.

---

## 2.3 Wallets for Development

A wallet manages the private keys used to sign transactions. For development, browser extension wallets like MetaMask are the most common way to interact with test networks and local blockchains from a browser-based dApp. Development wallets should never hold real funds — dedicated test accounts with test-network ("testnet") ETH are used instead, so mistakes carry no financial risk.

---

## 2.4 Editors and Extensions

Any general-purpose code editor works, but Visual Studio Code is the most widely used in the Solidity ecosystem, largely due to extensions like Solidity syntax highlighting, inline compiler error checking, and integrated Hardhat/Foundry support. A typical project also includes a `.env` file (kept out of version control) for storing RPC URLs and private keys used by scripts.

---

## 2.5 Setting Up a Project

A minimal setup looks like:

1. Install Node.js and a package manager.
2. Initialize a project (`npm init` or a framework's `init` command, e.g. `npx hardhat init` or `forge init`).
3. Install the chosen framework and any needed libraries (e.g. OpenZeppelin contracts).
4. Configure a network connection (an RPC URL from a provider such as Alchemy or Infura, or a local node) for compiling and deploying.

This environment is what every later lesson — from writing your first contract to deploying on mainnet — builds on top of.

[Previous](./[1]-What-is-Blockchain.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[3]-Anatomy-of-a-Blockchain-Project.md)
