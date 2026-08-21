[Previous](./[31]-Oracles-and-Off-Chain-Data.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[33]-Cross-Chain-Bridges-and-Interoperability.md)

*Decentralized Systems*

# Lesson 32 - Layer 2 Solutions & Scaling

## 32.1 The Scalability Problem

Ethereum's mainnet (Layer 1) processes a limited number of transactions per second, since every node must independently verify every transaction to maintain decentralization and security — a constraint often summarized as the "blockchain trilemma," a difficult trade-off between decentralization, security, and scalability. When demand exceeds capacity, transaction fees rise sharply.

---

## 32.2 What Are Layer 2s?

Layer 2 (L2) networks process transactions off of Ethereum's main chain while still relying on it for final security and data availability. This lets them offer much higher throughput and lower fees, while still inheriting most of Ethereum's security guarantees, rather than starting a brand-new, independently-secured blockchain.

---

## 32.3 Rollups

Rollups are the dominant L2 scaling approach today. They execute transactions off-chain, then post compressed transaction data (and a proof of correctness) back to Ethereum mainnet:

- **Optimistic Rollups** (e.g. Arbitrum, Optimism) — assume transactions are valid by default, but allow a challenge period during which anyone can submit fraud proofs to dispute an invalid state transition.
- **Zero-Knowledge (ZK) Rollups** (e.g. zkSync, Starknet, Polygon zkEVM) — attach a cryptographic proof to every batch showing the new state is correct, without needing a challenge period, though generating these proofs is computationally intensive.

---

## 32.4 Sidechains and Other Scaling Approaches

**Sidechains** (e.g. Polygon PoS) are independent blockchains connected to Ethereum via a bridge, but with their own separate consensus and security model — offering higher throughput, but without directly inheriting Ethereum's security. **State channels** let participants transact off-chain and only settle a final result on-chain, useful for high-frequency interactions between a known set of parties.

---

## 32.5 Trade-offs

Moving activity to L2s generally means faster, cheaper transactions, at the cost of some additional complexity (bridging assets between layers, withdrawal delays on optimistic rollups) and, in some designs, additional trust assumptions compared to Ethereum mainnet itself. Choosing an L2 for a given application involves weighing these trade-offs against the application's specific needs for speed, cost, and security.

[Previous](./[31]-Oracles-and-Off-Chain-Data.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[33]-Cross-Chain-Bridges-and-Interoperability.md)
