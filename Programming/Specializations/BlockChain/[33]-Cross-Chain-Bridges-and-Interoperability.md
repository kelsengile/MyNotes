[Previous](./[32]-Layer-2-Solutions-and-Scaling.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[34]-Decentralized-Finance-Basics.md)

*Decentralized Systems*

# Lesson 33 - Cross-Chain Bridges & Interoperability

## 33.1 Why Bridges Exist

Blockchains are independent, isolated systems by default — an asset native to Ethereum doesn't natively exist on, say, Solana or Arbitrum. A **bridge** is infrastructure that lets assets or data move between otherwise separate chains, enabling users to access liquidity and applications across multiple ecosystems.

---

## 33.2 Lock-and-Mint Bridges

The most common bridge model locks the original asset in a contract on the source chain and mints an equivalent, redeemable "wrapped" representation on the destination chain. Reversing the process (burning the wrapped token) unlocks the original asset back on the source chain.

```
User locks 1 ETH on Ethereum
   -> Bridge mints 1 "Bridged ETH" on the destination chain
   -> User later burns Bridged ETH to unlock the original ETH
```

---

## 33.3 Bridge Security Models

- **Trusted (federated) bridges** — rely on a set of designated validators or a multi-signature wallet to approve transfers; faster, but introduces trust in that specific group.
- **Trust-minimized bridges** — use light clients or cryptographic proofs to verify events on the source chain without relying on a specific trusted party, offering stronger security guarantees at the cost of complexity and often higher latency.

Bridges have historically been one of the most exploited categories of infrastructure in the industry, since they concentrate large amounts of locked value behind a relatively small and often complex piece of code.

---

## 33.4 Interoperability Protocols

Beyond simple asset bridges, protocols like Chainlink's CCIP (Cross-Chain Interoperability Protocol) and LayerZero aim to provide general-purpose cross-chain messaging — letting a contract on one chain trigger logic or share data with a contract on a different chain — extending interoperability beyond just moving tokens to enabling fully cross-chain applications.

[Previous](./[32]-Layer-2-Solutions-and-Scaling.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[34]-Decentralized-Finance-Basics.md)
