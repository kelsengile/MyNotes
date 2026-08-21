[Previous](./[7]-Structs-Arrays-and-Mappings.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[9]-Consensus-Mechanisms.md)

*Blockchain Fundamentals*

# Lesson 8 - How Blocks & Chains Work

## 8.1 What's Inside a Block

A block bundles together a batch of transactions along with metadata: a timestamp, a reference (hash) to the previous block, a block number, and a summary hash of its own transactions (often a Merkle root). This metadata is what turns a simple list of transactions into a verifiable, ordered chain.

---

## 8.2 Linking Blocks Together

Each block contains the cryptographic hash of the block before it. Because a hash changes completely if even one bit of input data changes, altering any transaction in an old block would change that block's hash, which would break the link stored in the next block, and the next, all the way to the tip of the chain. This is what makes the "chain" in blockchain tamper-evident.

---

## 8.3 Merkle Trees

Rather than hashing every transaction in a block together as one giant blob, blocks typically organize their transactions into a Merkle tree: transactions are hashed in pairs, those hashes are hashed in pairs again, and so on up to a single **Merkle root** stored in the block header. This structure lets anyone efficiently prove that a specific transaction is included in a block without downloading every transaction in it — a technique called a Merkle proof.

---

## 8.4 The Genesis Block and Chain Growth

The very first block in a blockchain, called the genesis block, has no predecessor. From there, nodes continuously propose and validate new blocks according to the network's consensus rules (covered in the next lesson), each one extending the chain. When two valid blocks are proposed around the same time, temporary forks can occur; the network's consensus rules determine which fork nodes ultimately treat as the canonical chain.

[Previous](./[7]-Structs-Arrays-and-Mappings.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[9]-Consensus-Mechanisms.md)
