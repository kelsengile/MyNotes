[Previous](./[15]-Deploying-Contracts-to-a-Testnet.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[17]-Error-Handling-and-Require-Revert.md)

*Smart Contracts*

# Lesson 16 - Contract Events & Logging

## 16.1 Why Events Exist

Smart contract storage is expensive to read from off-chain in bulk, and there's no built-in way for a contract to "push" data to a front-end. Events solve this: they write structured logs to the transaction receipt, which are far cheaper than storage and can be efficiently queried and subscribed to by off-chain applications.

---

## 16.2 Declaring and Emitting Events

Events are declared with the `event` keyword and triggered with `emit`:

```solidity
event Transfer(address indexed from, address indexed to, uint256 amount);

function transfer(address to, uint256 amount) public {
    // ...transfer logic...
    emit Transfer(msg.sender, to, amount);
}
```

---

## 16.3 Indexed Parameters

Marking a parameter `indexed` (up to three per event) allows it to be efficiently filtered — for example, querying every `Transfer` event where `to` matches a specific address — without scanning every log. Non-indexed parameters are still recorded but can only be read after the fact, not filtered on directly by the node.

---

## 16.4 Listening for Events Off-Chain

Front-ends and back-end services commonly use libraries like Ethers.js to listen for events in real time or to query historical logs:

```javascript
contract.on("Transfer", (from, to, amount) => {
    console.log(`${from} sent ${amount} to ${to}`);
});
```

Events are also the backbone of indexing services like The Graph, which read logs from many blocks and organize them into queryable databases — essential for dApps that need to display historical activity without re-scanning the entire chain on every page load.

[Previous](./[15]-Deploying-Contracts-to-a-Testnet.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[17]-Error-Handling-and-Require-Revert.md)
