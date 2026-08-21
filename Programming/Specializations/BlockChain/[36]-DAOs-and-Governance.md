[Previous](./[35]-Decentralized-Exchanges-and-AMMs.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[37]-Testing-Frameworks.md)

*DeFi & Advanced Concepts*

# Lesson 36 - DAOs & Governance

## 36.1 What Is a DAO?

A Decentralized Autonomous Organization (DAO) is a group coordinated through rules encoded in smart contracts and enforced on-chain rather than through a traditional legal entity or centralized management structure. Members typically hold a **governance token** that grants them the ability to propose and vote on decisions affecting the organization or protocol.

---

## 36.2 Governance Tokens and Voting

Governance tokens are usually ERC-20 tokens where voting power is proportional to tokens held (or, in some designs, tokens staked/locked for a period). Basic on-chain voting looks like:

```solidity
function vote(uint256 proposalId, bool support) public {
    uint256 weight = governanceToken.balanceOf(msg.sender);
    require(weight > 0, "No voting power");
    proposals[proposalId].votes[support ? "for" : "against"] += weight;
}
```

Real DAO governance contracts (such as OpenZeppelin's `Governor` framework) add proposal thresholds, voting delays, quorum requirements, and timelocks on top of this basic model.

---

## 36.3 The Proposal Lifecycle

A typical DAO governance process follows: **proposal creation** (often requiring a minimum token threshold to prevent spam) → **voting period** (a fixed window during which token holders cast votes) → **quorum check** (ensuring enough participation for the result to be legitimate) → **timelock** (a mandatory delay before execution, giving members a chance to react to a passed but contentious proposal) → **execution** (the proposal's encoded action is automatically carried out on-chain).

---

## 36.4 Challenges in DAO Governance

DAOs face real practical challenges: low voter turnout can let a small, motivated minority control outcomes; large token holders ("whales") can dominate votes; and complex proposals are often difficult for average token holders to evaluate. Many DAOs experiment with delegation (letting token holders delegate their voting power to trusted representatives) and other mechanisms to address these issues, though no design has fully solved them.

[Previous](./[35]-Decentralized-Exchanges-and-AMMs.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[37]-Testing-Frameworks.md)
