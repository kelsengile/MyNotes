[Previous](./[30]-Decentralized-Storage-IPFS.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[32]-Layer-2-Solutions-and-Scaling.md)

*Decentralized Systems*

# Lesson 31 - Oracles & Off-Chain Data (Chainlink)

## 31.1 The Oracle Problem

Smart contracts can only access data that already exists on their own blockchain — they have no native way to fetch a stock price, weather data, sports scores, or even the price of another cryptocurrency from off-chain. An **oracle** is a service that brings external, real-world data on-chain so contracts can use it. The core challenge, known as "the oracle problem," is that if a single, centralized party controls that data feed, they become a single point of failure and potential manipulation for every contract relying on it.

---

## 31.2 Decentralized Oracle Networks

**Chainlink** is the most widely used decentralized oracle network: rather than trusting one data source, it aggregates data from multiple independent node operators, who are economically incentivized (and can be penalized) to report accurate data, before delivering a single aggregated value on-chain.

```solidity
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

AggregatorV3Interface internal priceFeed;

function getLatestPrice() public view returns (int256) {
    (, int256 price,,,) = priceFeed.latestRoundData();
    return price;
}
```

---

## 31.3 Common Oracle Use Cases

- **Price feeds** — real-time asset prices, critical for lending protocols and derivatives to determine collateral value.
- **Verifiable randomness** — Chainlink VRF provides tamper-proof random numbers for use cases like NFT trait assignment or on-chain games, which the deterministic EVM cannot generate securely on its own.
- **Automation (keepers)** — services that trigger contract functions on a schedule or under certain conditions, since contracts cannot execute themselves without an external transaction.
- **Cross-chain messaging** — passing data or instructions between different blockchains.

---

## 31.4 Risks of Oracle Dependence

Even decentralized oracles introduce trust assumptions and potential attack surfaces — as covered in Lesson 27, manipulated or delayed price data has caused some of the largest exploits in DeFi history. Best practice includes using time-weighted average prices, multiple independent data sources, and sanity checks on returned values rather than trusting a single oracle call blindly.

[Previous](./[30]-Decentralized-Storage-IPFS.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[32]-Layer-2-Solutions-and-Scaling.md)
