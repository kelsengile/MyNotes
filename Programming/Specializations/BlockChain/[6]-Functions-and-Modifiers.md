[Previous](./%5B5%5D-Control-Flow%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[7]-Structs-Arrays-and-Mappings.md)

*Core Syntax*

# Lesson 6 - Functions & Modifiers

## 6.1 Function Anatomy

A Solidity function declares a name, parameters, visibility, optional state mutability, and optional return values:

```solidity
function transfer(address to, uint256 amount) public returns (bool) {
    // logic
    return true;
}
```

---

## 6.2 Visibility

- `public` — callable from anywhere, inside or outside the contract.
- `external` — callable only from outside the contract; slightly cheaper for large inputs since parameters are read directly from calldata.
- `internal` — callable only from within the contract or contracts that inherit from it.
- `private` — callable only from within the exact contract it's defined in, not even by child contracts.

---

## 6.3 State Mutability

- `view` — reads state but does not modify it; can be called for free off-chain.
- `pure` — neither reads nor modifies state; also free to call off-chain.
- `payable` — allows the function to receive ETH along with the call.
- (default, no keyword) — the function may modify state and requires a transaction (and gas) to execute.

---

## 6.4 Modifiers

A modifier wraps reusable logic — most commonly a `require` check — around a function's body, using the special `_;` placeholder to mark where the wrapped function's code should run:

```solidity
modifier onlyOwner() {
    require(msg.sender == owner, "Not the owner");
    _;
}

function withdraw() public onlyOwner {
    // only executes if the modifier's require passes
}
```

Modifiers are the standard way to enforce access control and validation without repeating the same checks in every function.

---

## 6.5 Function Overloading and Return Values

Solidity allows multiple functions with the same name as long as their parameter types differ (overloading). Functions can also return multiple values at once, which the caller can unpack:

```solidity
function getBalanceInfo() public view returns (uint256 balance, bool isPositive) {
    return (address(this).balance, address(this).balance > 0);
}
```

[Previous](./%5B5%5D-Control-Flow%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[7]-Structs-Arrays-and-Mappings.md)
