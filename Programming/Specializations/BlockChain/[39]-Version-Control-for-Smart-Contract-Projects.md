[Previous](./[38]-Local-Blockchain-Networks-and-Forking-Mainnet.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[40]-Deploying-to-Mainnet.md)

*Tooling & Testing*

# Lesson 39 - Version Control for Smart Contract Projects

## 39.1 Why Version Control Matters More Here

In most software, a bad commit can be fixed with another deployment. In blockchain development, a bug shipped to a live, immutable contract can be permanent and can directly cost users real money. Careful version control — clear commit history, protected branches, and mandatory review before merging — is a critical safety practice, not just a convenience.

---

## 39.2 What to Exclude from Git

Smart contract projects generate compiled artifacts and depend on secrets that should never be committed:

```
# .gitignore
node_modules/
artifacts/
cache/
out/
.env
```

Committing a `.env` file containing a real private key is one of the most common — and most costly — mistakes new blockchain developers make; a leaked key can mean an immediate, irreversible loss of funds.

---

## 39.3 Branching and Review for Contract Changes

Given the stakes of deploying incorrect code, many teams enforce stricter review requirements for contract changes than typical application code: mandatory multiple-reviewer approval, required passing test suites and static analysis (Lesson 28) before merge, and a dedicated branch or tag pointing to exactly what code was audited and deployed to mainnet.

---

## 39.4 Tracking Deployed Addresses

Because a contract's address is permanent once deployed, most projects keep a dedicated record — often a JSON file or a dedicated `deployments/` folder — mapping each network (mainnet, Sepolia, etc.) to the exact contract addresses deployed there, along with the commit hash of the source code that was deployed. This traceability is essential for verification (Lesson 41) and for anyone auditing what code is actually live at a given address.

[Previous](./[38]-Local-Blockchain-Networks-and-Forking-Mainnet.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[40]-Deploying-to-Mainnet.md)
