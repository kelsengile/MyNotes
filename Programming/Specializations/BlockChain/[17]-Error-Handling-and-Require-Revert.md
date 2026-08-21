[Previous](./[16]-Contract-Events-and-Logging.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[18]-Upgradeable-Contracts-and-Proxies.md)

*Smart Contracts*

# Lesson 17 - Error Handling & Require/Revert

## 17.1 require

`require` checks a condition and reverts the entire transaction (undoing all state changes made so far) with an optional error message if it's false. It's most commonly used to validate function inputs and access control at the start of a function:

```solidity
function withdraw(uint256 amount) public {
    require(amount <= balances[msg.sender], "Insufficient balance");
    balances[msg.sender] -= amount;
    payable(msg.sender).transfer(amount);
}
```

---

## 17.2 revert and Custom Errors

`revert` immediately halts execution and reverts state, and can be combined with `require`-style logic in more complex conditionals. Since Solidity 0.8.4, **custom errors** offer a gas-cheaper alternative to string messages, since they avoid storing a full string in the bytecode:

```solidity
error InsufficientBalance(uint256 requested, uint256 available);

function withdraw(uint256 amount) public {
    if (amount > balances[msg.sender]) {
        revert InsufficientBalance(amount, balances[msg.sender]);
    }
    // ...
}
```

---

## 17.3 assert

`assert` is meant for checking internal invariants — conditions that should *never* be false if the contract is correct. Unlike `require`, a failing `assert` signals a bug in the contract logic itself, not invalid user input. Modern Solidity treats a failed `assert` similarly to a `revert`, consuming the remaining gas provided in older compiler versions before 0.8.0.

---

## 17.4 try/catch

When calling another contract, `try`/`catch` allows a contract to handle a failed external call gracefully instead of having the failure automatically revert the whole transaction:

```solidity
try externalContract.riskyCall() returns (uint256 result) {
    // handle success
} catch {
    // handle failure without reverting this transaction
}
```

This is particularly useful when interacting with external, untrusted, or third-party contracts whose failure shouldn't necessarily break the calling contract's own logic.

[Previous](./[16]-Contract-Events-and-Logging.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[18]-Upgradeable-Contracts-and-Proxies.md)
