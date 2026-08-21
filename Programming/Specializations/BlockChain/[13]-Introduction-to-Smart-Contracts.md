[Previous](./[12]-Transactions-and-Gas.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[14]-Writing-Your-First-Smart-Contract.md)

*Smart Contracts*

# Lesson 13 - Introduction to Smart Contracts

## 13.1 What Is a Smart Contract?

A smart contract is a program deployed on a blockchain that executes exactly as written, with no possibility of downtime, censorship, or third-party interference. Rather than a legal document, it's executable code: once deployed, its logic runs deterministically whenever it's called, and its rules can't be changed unless the contract was specifically designed to allow upgrades (see Lesson 18).

---

## 13.2 The Ethereum Virtual Machine (EVM)

The EVM is the runtime environment that executes smart contract bytecode. Every node on the network runs the same EVM and re-executes every transaction, which is how the network reaches agreement on the resulting state. The EVM is Turing-complete (it can run arbitrary logic), but every operation costs gas specifically to prevent infinite loops from stalling the network.

---

## 13.3 Contract Deployment and Addresses

Deploying a contract means sending a special transaction whose "to" field is empty and whose data is the contract's compiled bytecode. The network executes the contract's constructor once, and assigns the contract a permanent address, derived from the deployer's address and their transaction nonce (or, for some deployment patterns, from a salt and the contract's bytecode).

---

## 13.4 What Smart Contracts Enable

Smart contracts are the foundation for nearly everything covered later in this course: fungible and non-fungible tokens, decentralized exchanges, lending protocols, DAOs, and NFT marketplaces are all, at their core, smart contracts that hold logic and, often, funds. Because a deployed contract's code is public and its behavior is enforced by the network rather than a company, users can verify exactly what it will do before interacting with it.

[Previous](./[12]-Transactions-and-Gas.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[14]-Writing-Your-First-Smart-Contract.md)
