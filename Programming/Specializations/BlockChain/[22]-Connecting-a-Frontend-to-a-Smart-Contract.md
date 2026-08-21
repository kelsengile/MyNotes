[Previous](./[21]-Token-Minting-and-Burning.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[23]-Reading-and-Writing-On-Chain-Data.md)

*Decentralized Applications (dApps)*

# Lesson 22 - Connecting a Front-End to a Smart Contract (Web3.js/Ethers.js)

## 22.1 What a Web3 Library Does

A Web3 library bridges JavaScript running in a browser (or Node.js) with an Ethereum node, translating function calls into JSON-RPC requests and decoding responses using a contract's ABI. **Ethers.js** and **Web3.js** are the two most widely used libraries; Ethers.js has become the more common default in newer projects for its lighter footprint and cleaner API.

---

## 22.2 Providers and Signers

- A **provider** is a read-only connection to the blockchain, used for querying balances, calling `view` functions, and reading past events.
- A **signer** wraps a provider with access to a private key (typically via a connected wallet like MetaMask), allowing it to sign and send transactions.

```javascript
import { BrowserProvider, Contract } from "ethers";

const provider = new BrowserProvider(window.ethereum);
const signer = await provider.getSigner();
```

---

## 22.3 Instantiating a Contract

With an ABI and address, a JavaScript object can be created that mirrors the contract's functions, letting front-end code call them almost like local functions:

```javascript
const contract = new Contract(contractAddress, contractABI, signer);

const balance = await contract.balanceOf(userAddress); // read
const tx = await contract.transfer(recipient, amount);  // write
await tx.wait(); // wait for confirmation
```

---

## 22.4 Read vs. Write Calls

Calling a `view`/`pure` function returns data instantly and costs no gas, since it's simulated locally by the node rather than broadcast to the network. Calling a state-changing function sends an actual transaction, requires the user's wallet to sign it, costs gas, and only resolves once it's mined and confirmed — a distinction front-end code must handle with different loading and error states.

[Previous](./[21]-Token-Minting-and-Burning.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[23]-Reading-and-Writing-On-Chain-Data.md)
