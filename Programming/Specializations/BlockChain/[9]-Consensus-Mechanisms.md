[Previous](./[8]-How-Blocks-and-Chains-Work.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[10]-Cryptographic-Hashing-and-Digital-Signatures.md)

*Blockchain Fundamentals*

# Lesson 9 - Consensus Mechanisms (PoW, PoS)

## 9.1 Why Consensus Is Needed

In a decentralized network with no central authority, nodes need a way to agree on a single, shared history of transactions — especially to prevent "double-spending," where the same funds are used twice. Consensus mechanisms are the rules that decide who gets to propose the next block and how the network agrees it's valid.

---

## 9.2 Proof of Work (PoW)

Proof of Work, used by Bitcoin and (until 2022) Ethereum, requires participants called **miners** to compete to solve a computationally expensive puzzle (finding a hash below a target value). The first miner to solve it gets to propose the next block and receives a block reward. This makes attacking the network expensive, since a would-be attacker would need to out-compute the rest of the network's combined hardware, but it consumes large amounts of electricity.

---

## 9.3 Proof of Stake (PoS)

Proof of Stake replaces computational competition with economic collateral. Participants called **validators** lock up ("stake") the network's native asset as collateral, and the protocol pseudo-randomly selects a validator to propose each block, weighted by stake size. Validators that act dishonestly can have a portion of their stake destroyed, a penalty called "slashing." Ethereum transitioned from PoW to PoS in September 2022 in an event known as "The Merge," dramatically cutting the network's energy use.

---

## 9.4 Comparing the Two

| | Proof of Work | Proof of Stake |
|---|---|---|
| Resource spent | Computational power / electricity | Staked capital |
| Entry cost | Mining hardware | Minimum stake amount |
| Energy use | High | Comparatively low |
| Examples | Bitcoin, pre-2022 Ethereum | Ethereum, Cardano, Solana |

Other consensus variants exist too, including Delegated Proof of Stake and Proof of Authority, generally trading off some decentralization for higher transaction throughput.

[Previous](./[8]-How-Blocks-and-Chains-Work.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[10]-Cryptographic-Hashing-and-Digital-Signatures.md)
