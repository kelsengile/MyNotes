[Previous](./[14]-Writing-Your-First-Smart-Contract.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[16]-Contract-Events-and-Logging.md)

*Smart Contracts*

# Lesson 15 - Deploying Contracts to a Testnet

## 15.1 What Are Testnets?

Testnets are separate blockchain networks that mimic mainnet behavior but use tokens with no real-world value, making them safe environments to deploy and test contracts. Ethereum's primary public testnet is **Sepolia**; **Holesky** is used for staking and validator-focused testing. Using a testnet lets developers catch bugs and gas issues before risking real funds on mainnet.

---

## 15.2 Getting Test ETH

Test networks require test ETH to pay for gas, which is obtained for free from a **faucet** — a website or service that sends small amounts of testnet ETH to a specified address, sometimes requiring proof of activity (like a mainnet transaction history) to prevent abuse.

---

## 15.3 Connecting to a Testnet

To deploy beyond a local machine, a project needs an RPC (Remote Procedure Call) endpoint for the target network, typically provided by a node provider like Alchemy or Infura. This URL, along with a deployer private key (stored securely, e.g. in an untracked `.env` file), is configured in the project's `hardhat.config.js` or `foundry.toml`.

```javascript
// hardhat.config.js (excerpt)
networks: {
  sepolia: {
    url: process.env.SEPOLIA_RPC_URL,
    accounts: [process.env.PRIVATE_KEY]
  }
}
```

---

## 15.4 Running a Deployment

A deployment script instantiates the contract factory and sends the deployment transaction:

```javascript
const factory = await ethers.getContractFactory("SimpleStorage");
const contract = await factory.deploy();
await contract.waitForDeployment();
console.log("Deployed to:", await contract.getAddress());
```

Running this against a testnet (e.g. `npx hardhat run scripts/deploy.js --network sepolia`) produces a real, publicly viewable contract address on that network, which can then be inspected on a block explorer like Sepolia Etherscan.

[Previous](./[14]-Writing-Your-First-Smart-Contract.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[16]-Contract-Events-and-Logging.md)
