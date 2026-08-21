[Previous](./[22]-Connecting-a-Frontend-to-a-Smart-Contract.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[24]-Building-a-Simple-dApp-UI.md)

*Decentralized Applications (dApps)*

# Lesson 23 - Reading & Writing On-Chain Data

## 23.1 Reading Current State

Reading a contract's current state — a token balance, an owner, a stored value — is done by calling a `view` function through a provider. This is free and near-instant because it's executed locally by the connected node rather than broadcast as a transaction to the whole network.

---

## 23.2 Reading Historical Data with Events

Current state only shows the present; understanding history (like "all past transfers to this address") requires querying **event logs** (see Lesson 16) rather than contract storage. For anything beyond a small, recent range of blocks, most production dApps use an indexer such as The Graph, which pre-processes logs into an efficient, queryable API instead of scanning raw blocks live.

```javascript
const filter = contract.filters.Transfer(null, userAddress);
const events = await contract.queryFilter(filter, fromBlock, toBlock);
```

---

## 23.3 Writing Data (Sending Transactions)

Writing data means sending a transaction that calls a state-changing function, which must be signed by a wallet, costs gas, and isn't final until mined:

```javascript
const tx = await contract.setValue(42);
const receipt = await tx.wait(); // waits for confirmation
console.log("Confirmed in block:", receipt.blockNumber);
```

---

## 23.4 Handling Confirmation and Finality

A transaction included in a block is not immediately irreversible — a chain reorganization could still remove it in rare cases, especially soon after inclusion. Applications handling significant value typically wait for multiple block confirmations before treating a transaction as final, and should always handle the possibility that a submitted transaction reverts or is dropped from the mempool entirely.

[Previous](./[22]-Connecting-a-Frontend-to-a-Smart-Contract.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[24]-Building-a-Simple-dApp-UI.md)
