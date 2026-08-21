[Previous](./[13]-Introduction-to-Smart-Contracts.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[15]-Deploying-Contracts-to-a-Testnet.md)

*Smart Contracts*

# Lesson 14 - Writing Your First Smart Contract (Solidity)

## 14.1 Contract Skeleton

Every Solidity file starts with an SPDX license identifier and a pragma statement declaring the compiler version, followed by a `contract` declaration:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleStorage {
    uint256 private storedValue;

    function set(uint256 value) public {
        storedValue = value;
    }

    function get() public view returns (uint256) {
        return storedValue;
    }
}
```

---

## 14.2 The Constructor

A `constructor` runs exactly once, at deployment, and is typically used to set initial state such as an owner address or starting supply:

```solidity
contract Owned {
    address public owner;

    constructor() {
        owner = msg.sender;
    }
}
```

---

## 14.3 Global Variables

Solidity exposes special global variables giving access to information about the current transaction and blockchain state:

- `msg.sender` — the address that called the current function.
- `msg.value` — the amount of ETH sent with the call.
- `block.timestamp` — the timestamp of the current block.
- `address(this)` — the contract's own address.

These are used constantly for access control, payment logic, and time-based conditions.

---

## 14.4 Compiling and Running Locally

Using Hardhat, a contract is compiled with `npx hardhat compile`, which produces the ABI and bytecode in the `artifacts/` folder. From there it can be deployed to Hardhat's built-in local network and interacted with using scripts or the Hardhat console — a fast, free way to test contract logic before touching a real network. Foundry equivalents are `forge build` for compiling and `anvil` for a local node.

[Previous](./[13]-Introduction-to-Smart-Contracts.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[15]-Deploying-Contracts-to-a-Testnet.md)
