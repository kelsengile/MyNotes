[Previous](./%5B4%5D-Variables-and-Data-Types%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[6]-Functions-and-Modifiers.md)

*Core Syntax*

# Lesson 5 - Control Flow: Conditionals & Loops

## 5.1 Conditionals

Solidity supports familiar `if` / `else if` / `else` statements for branching logic:

```solidity
function classify(uint256 x) public pure returns (string memory) {
    if (x == 0) {
        return "zero";
    } else if (x < 100) {
        return "small";
    } else {
        return "large";
    }
}
```

Conditions must evaluate to a `bool` — unlike some languages, Solidity does not allow implicit conversion of numbers to booleans.

---

## 5.2 Loops

`for`, `while`, and `do-while` loops all exist in Solidity and behave much like their JavaScript or C equivalents. However, every loop iteration consumes gas, so loops over data whose size is controlled by users (for example, an array that grows without bound) are a common source of an "out of gas" failure or a denial-of-service vulnerability, since a transaction that runs out of gas mid-execution reverts entirely.

```solidity
function sumArray(uint256[] memory nums) public pure returns (uint256) {
    uint256 total = 0;
    for (uint256 i = 0; i < nums.length; i++) {
        total += nums[i];
    }
    return total;
}
```

---

## 5.3 Gas-Aware Control Flow

Because every computational step costs gas, Solidity developers favor patterns that keep loops bounded and predictable: pagination, capping array sizes, or moving iteration off-chain wherever possible. Unlike a typical backend language, an infinite or unexpectedly long loop is not just slow — it can make a function permanently uncallable once its gas cost exceeds the network's block gas limit.

---

## 5.4 Short-Circuiting and Early Returns

Logical operators `&&` and `||` short-circuit just as in JavaScript, and `return` can be used to exit a function early. Combined with `require` statements (covered in Lesson 17), early returns and early reverts are the standard way to keep contract logic simple and to avoid unnecessary gas consumption on invalid inputs.

[Previous](./%5B4%5D-Variables-and-Data-Types%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[6]-Functions-and-Modifiers.md)
