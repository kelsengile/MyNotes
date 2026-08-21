[Previous](./[11]-Wallets-Keys-and-Addresses.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[13]-Introduction-to-Smart-Contracts.md)

*Blockchain Fundamentals*

# Lesson 12 - Transactions & Gas

## 12.1 Anatomy of a Transaction

An Ethereum transaction is a signed piece of data that typically includes: the sender's address (derived from the signature), a recipient (a wallet or contract address), an amount of ETH to transfer, optional data (used to call a contract function), a gas limit, a gas price (or, since EIP-1559, a base fee and priority fee), and a nonce (a counter that prevents the same transaction from being replayed).

---

## 12.2 What Is Gas?

Gas measures the computational work a transaction requires. Every EVM operation — storage writes, arithmetic, memory access — has a fixed gas cost. Before sending a transaction, users specify a gas limit (the maximum they're willing to pay for) and a price per unit of gas; if execution runs out of gas before completing, the entire transaction reverts, though the gas already consumed is still paid to the network.

---

## 12.3 EIP-1559 and Fee Structure

Since the EIP-1559 upgrade, Ethereum transactions have two fee components: a **base fee**, which is burned (destroyed) and adjusts automatically based on network demand, and a **priority fee** (tip), paid directly to the validator to incentivize faster inclusion. Total cost is roughly `gas used × (base fee + priority fee)`.

---

## 12.4 Transaction Lifecycle

1. A user signs a transaction with their private key.
2. It's broadcast to the network and enters the **mempool** (a pool of pending transactions).
3. A validator selects it for inclusion in a block, typically prioritizing higher-fee transactions.
4. The block is validated and added to the chain; the transaction is now "confirmed."
5. Additional blocks built on top increase confidence that the transaction won't be reverted by a chain reorganization.

Understanding gas is essential for smart contract development, since inefficient contract code directly translates into higher costs for every user who calls it — a theme covered further in Lesson 29 and Lesson 44.

[Previous](./[11]-Wallets-Keys-and-Addresses.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[13]-Introduction-to-Smart-Contracts.md)
