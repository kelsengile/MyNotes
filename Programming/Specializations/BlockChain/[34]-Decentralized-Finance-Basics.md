[Previous](./[33]-Cross-Chain-Bridges-and-Interoperability.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[35]-Decentralized-Exchanges-and-AMMs.md)

*DeFi & Advanced Concepts*

# Lesson 34 - Decentralized Finance (DeFi) Basics

## 34.1 What Is DeFi?

Decentralized Finance (DeFi) refers to financial applications — trading, lending, borrowing, derivatives, insurance — built as smart contracts rather than run by traditional intermediaries like banks or brokerages. Because the logic is transparent, permissionless code, anyone with a wallet can access these services directly, and different DeFi protocols can be freely composed together, often described as "money legos."

---

## 34.2 Lending and Borrowing

DeFi lending protocols (e.g. Aave, Compound) let users deposit assets to earn interest, and let others borrow against posted collateral. Because there's no credit-check mechanism to enforce off-chain repayment, loans are typically **overcollateralized** — a borrower must deposit more value than they borrow — and if collateral value falls too close to the loan value, it can be automatically **liquidated** by the protocol to protect lenders.

---

## 34.3 Stablecoins

Stablecoins are tokens designed to track the value of a stable reference asset, usually the US dollar, making them useful for payments and as a stable unit of account within volatile crypto markets. Common designs include:

- **Fiat-collateralized** — backed 1:1 by real-world dollar reserves (e.g. USDC).
- **Crypto-collateralized** — backed by overcollateralized crypto assets locked in a smart contract (e.g. DAI).
- **Algorithmic** — attempt to maintain their peg through supply adjustments and market incentives rather than direct collateral backing, a design that has proven considerably riskier historically.

---

## 34.4 Yield Farming and Liquidity Mining

Yield farming refers to strategies where users move capital between DeFi protocols to earn the highest possible returns, often from a combination of trading fees, interest, and additional token rewards ("liquidity mining") that protocols distribute to attract capital. These strategies can offer high returns but also carry compounded risks — smart contract bugs, impermanent loss (Lesson 35), and volatile token reward prices among them.

[Previous](./[33]-Cross-Chain-Bridges-and-Interoperability.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[35]-Decentralized-Exchanges-and-AMMs.md)
