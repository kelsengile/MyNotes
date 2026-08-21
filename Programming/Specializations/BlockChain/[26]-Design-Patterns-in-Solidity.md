[Previous](./[25]-Wallet-Integration.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[27]-Security-Vulnerabilities-and-Common-Exploits.md)

*Advanced Smart Contract Development*

# Lesson 26 - Design Patterns in Solidity

## 26.1 Checks-Effects-Interactions

This pattern orders a function's logic to minimize security risk: first **check** conditions (`require` statements), then **update** the contract's own state (**effects**), and only then make **external calls** to other contracts or addresses (**interactions**). Following this order prevents a large class of reentrancy bugs (covered in Lesson 27), since state is already updated before control is ever handed to an external contract.

```solidity
function withdraw(uint256 amount) public {
    require(balances[msg.sender] >= amount, "Insufficient balance"); // check
    balances[msg.sender] -= amount;                                   // effect
    payable(msg.sender).transfer(amount);                             // interaction
}
```

---

## 26.2 Access Control Patterns

- **Ownable** — a single address (`owner`) has elevated privileges; simple but centralizes power in one key.
- **Role-Based Access Control (RBAC)** — different addresses are granted specific named roles (`MINTER_ROLE`, `ADMIN_ROLE`), allowing finer-grained permissions than a single owner.
- **Multi-signature** — critical actions require approval from multiple independent parties before executing, reducing the risk of a single compromised key causing catastrophic damage.

---

## 26.3 Pull over Push Payments

Rather than a contract actively "pushing" ETH to recipients (which can fail or be exploited if the recipient is a malicious contract), the pull pattern has the contract record what each address is owed, and lets each recipient withdraw it themselves:

```solidity
mapping(address => uint256) public pendingWithdrawals;

function withdraw() public {
    uint256 amount = pendingWithdrawals[msg.sender];
    pendingWithdrawals[msg.sender] = 0;
    payable(msg.sender).transfer(amount);
}
```

This isolates any single recipient's failed or malicious behavior from affecting other users' ability to withdraw.

---

## 26.4 Factory Pattern

A factory contract deploys and keeps track of multiple instances of another contract, useful for protocols that need to spin up a new, independent contract per user or per market (for example, a new pool contract for each trading pair on a decentralized exchange).

[Previous](./[25]-Wallet-Integration.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[27]-Security-Vulnerabilities-and-Common-Exploits.md)
