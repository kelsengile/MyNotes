[Previous](./[28]-Auditing-and-Testing-Smart-Contracts.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[30]-Decentralized-Storage-IPFS.md)

*Advanced Smart Contract Development*

# Lesson 29 - Gas Optimization Techniques

## 29.1 Storage Is Expensive

Writing to contract storage (`SSTORE`) is one of the most expensive operations in the EVM, far costlier than memory or stack operations. Minimizing storage writes — for example, updating a state variable once at the end of a function instead of repeatedly inside a loop — is one of the highest-impact optimizations available.

---

## 29.2 Variable Packing

Storage is allocated in fixed-size 32-byte "slots." Multiple smaller variables (e.g. two `uint128` values, or a `uint96` and an `address`) declared adjacent to each other can be packed into a single slot, cutting storage costs roughly in half compared to each taking its own slot:

```solidity
// Less efficient: each takes its own 32-byte slot
uint256 a;
bool b;
address c;

// More efficient: bool and address pack together into fewer slots
address c;
bool b;
uint256 a;
```

---

## 29.3 Using calldata and immutable/constant

Marking external function parameters as `calldata` instead of `memory` avoids an unnecessary copy. Values that never change after deployment should be marked `constant` (baked into bytecode at compile time) or `immutable` (set once in the constructor, stored in bytecode) rather than as regular storage variables, since both avoid the cost of an `SLOAD` on every read.

---

## 29.4 Short-Circuiting and Caching

Ordering `require` conditions so cheaper checks run before expensive ones lets a failing transaction revert earlier, saving gas on invalid calls. Similarly, caching a storage variable in a local (memory/stack) variable when it's read multiple times within a function avoids repeated expensive `SLOAD` operations.

```solidity
uint256 length = myArray.length; // cache instead of reading myArray.length each iteration
for (uint256 i = 0; i < length; i++) { /* ... */ }
```

---

## 29.5 Measuring Gas Usage

Tools like Hardhat's gas reporter and Foundry's built-in gas snapshots let developers measure exactly how much gas each function costs and track regressions over time, turning gas optimization into a measurable, testable part of development rather than guesswork.

[Previous](./[28]-Auditing-and-Testing-Smart-Contracts.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[30]-Decentralized-Storage-IPFS.md)
