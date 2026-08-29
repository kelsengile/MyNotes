[Previous](./[3]-Anatomy-of-a-Blockchain-Project.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./%5B5%5D-Control-Flow%20%281%29.md)

*Core Syntax*

# Lesson 4 - Variables, Data Types & Operators

## 4.1 Value Types

Solidity is a statically-typed language, meaning every variable's type is fixed at compile time. The most common value types are:

- `uint256` / `int256` — unsigned and signed integers (the number is the bit size, e.g. `uint8`, `uint256`).
- `bool` — `true` or `false`.
- `address` — a 20-byte Ethereum address; `address payable` is a variant that can receive ETH.
- `bytes32` — fixed-size raw byte data, often used for hashes.

Value types are copied whenever they're assigned or passed to a function.

---

## 4.2 Reference Types

Reference types — `string`, `bytes`, arrays, structs, and mappings — don't copy their entire contents on assignment; instead they're passed by reference within certain contexts and require a **data location** keyword: `storage` (persistent, on-chain), `memory` (temporary, exists only during a function call), or `calldata` (read-only, used for external function inputs, and the cheapest option gas-wise).

---

## 4.3 State Variables vs. Local Variables

**State variables** are declared at the contract level and are permanently stored on the blockchain — reading them is cheap, but writing to them costs gas. **Local variables** are declared inside a function and only exist while that function executes; they live in `memory` or the stack and disappear afterward.

```solidity
contract Example {
    uint256 public totalSupply; // state variable

    function double(uint256 x) public pure returns (uint256) {
        uint256 result = x * 2; // local variable
        return result;
    }
}
```

---

## 4.4 Operators

Solidity supports the arithmetic (`+ - * / %`), comparison (`== != < > <= >=`), and logical (`&& || !`) operators found in most C-like languages, plus bitwise operators (`& | ^ ~ << >>`). Since Solidity 0.8.0, arithmetic operations automatically revert on overflow/underflow instead of silently wrapping around, which removed the need for the manual "SafeMath" checks earlier versions required.

[Previous](./[3]-Anatomy-of-a-Blockchain-Project.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./%5B5%5D-Control-Flow%20%281%29.md)
