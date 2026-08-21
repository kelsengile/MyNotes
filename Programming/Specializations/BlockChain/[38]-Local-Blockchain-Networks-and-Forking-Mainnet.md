[Previous](./[37]-Testing-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[39]-Version-Control-for-Smart-Contract-Projects.md)

*Tooling & Testing*

# Lesson 38 - Local Blockchain Networks & Forking Mainnet

## 38.1 Why Use a Local Network?

A local blockchain network (Hardhat Network or Anvil) runs entirely on a developer's machine, providing instant transaction confirmation, free "test" ETH, and full control over blockchain state — ideal for fast iteration during development, without the delay or cost of a real testnet.

---

## 38.2 Useful Local Network Features

Local nodes typically expose extra capabilities real networks don't allow, extremely useful for testing:

- **Time travel** — advancing the local blockchain's timestamp to test time-dependent logic (like a vesting schedule) without waiting in real time.
- **Impersonation** — acting as any address, including ones the developer doesn't control the private key for, to test how a function behaves for a specific user.
- **Snapshots** — saving and reverting blockchain state instantly, useful for resetting between test cases.

---

## 38.3 Forking Mainnet

Rather than starting from an empty blockchain, a local node can be configured to **fork** mainnet (or another live network) at a specific block — copying its entire state locally, including real deployed contracts and token balances. This lets developers test how their contract interacts with real, live protocols (like an actual deployed Uniswap pool) without spending real funds or affecting the live network.

```javascript
// hardhat.config.js
networks: {
  hardhat: {
    forking: {
      url: process.env.MAINNET_RPC_URL,
      blockNumber: 18500000
    }
  }
}
```

---

## 38.4 When Forking Is Essential

Forking is especially valuable when a contract needs to integrate with existing, immutable, already-deployed protocols — for example, testing a new lending strategy against a real price oracle or liquidity pool — since redeploying accurate mock versions of complex, battle-tested protocols would be both time-consuming and less realistic than testing against the real thing.

[Previous](./[37]-Testing-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[39]-Version-Control-for-Smart-Contract-Projects.md)
