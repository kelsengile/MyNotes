[Previous](./[29]-Gas-Optimization-Techniques.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[31]-Oracles-and-Off-Chain-Data.md)

*Decentralized Systems*

# Lesson 30 - Decentralized Storage (IPFS)

## 30.1 Why Not Store Everything On-Chain?

On-chain storage is extremely expensive (see Lesson 29) and blockchains aren't designed to hold large files like images, videos, or documents efficiently. Decentralized storage networks let applications store larger data off-chain while keeping only a small, tamper-evident reference to it on-chain.

---

## 30.2 What Is IPFS?

The InterPlanetary File System (IPFS) is a peer-to-peer protocol for storing and sharing files. Instead of addressing content by *location* (like a URL), IPFS addresses content by its *hash* (a Content Identifier, or CID) — meaning the same content always produces the same CID no matter which node hosts it, and any change to the content produces a completely different CID.

---

## 30.3 How Content Addressing Ties into Smart Contracts

An NFT contract commonly stores only a `tokenURI` string on-chain, pointing to a CID like `ipfs://Qm.../metadata.json`. Because the CID is derived from the content's own hash, anyone can verify that the metadata retrieved from IPFS actually matches what the CID claims — a form of tamper-evidence similar to how block hashes secure the chain itself.

---

## 30.4 Pinning and Persistence

IPFS doesn't guarantee a file stays available forever — nodes only keep data they choose to "pin." If no node pins a file, it can become unreachable. Because of this, most production dApps rely on a pinning service (such as Pinata or Infura's IPFS service) or run their own dedicated node to ensure their content remains available, rather than assuming IPFS alone guarantees permanence.

---

## 30.5 Other Decentralized Storage Options

- **Arweave** — designed for one-time-payment, permanent storage, aimed at data meant to persist indefinitely.
- **Filecoin** — built on IPFS's addressing model but adds an incentive layer, paying storage providers to reliably store data over time.

These systems are frequently used together with IPFS-style content addressing, chosen based on whether an application needs guaranteed long-term persistence versus simple content-addressed storage.

[Previous](./[29]-Gas-Optimization-Techniques.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[31]-Oracles-and-Off-Chain-Data.md)
