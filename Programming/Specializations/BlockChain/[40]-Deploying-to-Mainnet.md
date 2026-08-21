[Previous](./[39]-Version-Control-for-Smart-Contract-Projects.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[41]-Contract-Verification-and-Documentation.md)

*Deployment & Production*

# Lesson 40 - Deploying to Mainnet

## 40.1 Pre-Deployment Checklist

Deploying to mainnet is effectively irreversible for most contracts, so it should never be treated casually. A responsible pre-deployment checklist includes: full test coverage passing, static analysis run and reviewed (Lesson 28), a completed security audit for anything holding meaningful value (Lesson 28), confirmed correct constructor parameters, and a final review of exactly which network the deployment script is configured to target.

---

## 40.2 The Cost of Mainnet Deployment

Unlike testnets, mainnet deployment costs real ETH, and gas prices fluctuate based on network demand — deploying during periods of high congestion can be significantly more expensive than during quieter periods. Contract size also directly affects deployment cost, since deploying is itself a transaction whose gas cost scales with the bytecode being stored.

---

## 40.3 Using a Secure Deployer Key

For mainnet deployments, using a hardware wallet or a secure secrets manager instead of a plaintext private key in an `.env` file is standard best practice, given the amount of value often at stake. Many teams also use a multi-signature wallet as the contract's owner/admin from the moment of deployment, rather than transferring ownership to it as an afterthought.

---

## 40.4 Post-Deployment Steps

After deploying, typical next steps include: verifying the contract's source code on a block explorer (Lesson 41), transferring any administrative privileges to their intended final owner (such as a multisig or DAO timelock), and recording the deployed address and deployment transaction in the project's documentation and deployment records (Lesson 39).

[Previous](./[39]-Version-Control-for-Smart-Contract-Projects.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[41]-Contract-Verification-and-Documentation.md)
