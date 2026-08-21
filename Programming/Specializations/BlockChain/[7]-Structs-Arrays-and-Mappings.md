[Previous](./[6]-Functions-and-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[8]-How-Blocks-and-Chains-Work.md)

*Core Syntax*

# Lesson 7 - Structs, Arrays & Mappings

## 7.1 Structs

A `struct` groups related values into a single custom type, similar to an object or record in other languages:

```solidity
struct User {
    address wallet;
    uint256 balance;
    bool isActive;
}

User public admin;
```

Structs are useful anywhere a contract needs to bundle multiple pieces of related data together, such as an order, a proposal, or a user profile.

---

## 7.2 Arrays

Solidity supports fixed-size arrays (`uint256[5]`) and dynamic arrays (`uint256[]`). Dynamic storage arrays support `.push()` and `.pop()`, and their length can be read with `.length`. Storage arrays are gas-expensive to grow or iterate over, so many contracts prefer mappings for large or user-controlled collections.

```solidity
uint256[] public scores;

function addScore(uint256 s) public {
    scores.push(s);
}
```

---

## 7.3 Mappings

A `mapping(KeyType => ValueType)` is a hash table stored on-chain, giving constant-time lookups by key. Mappings cannot be iterated over directly and have no concept of length — if a contract needs to enumerate all keys, it typically maintains a separate array of keys alongside the mapping.

```solidity
mapping(address => uint256) public balances;

function deposit() public payable {
    balances[msg.sender] += msg.value;
}
```

---

## 7.4 Combining Them

Real contracts frequently nest these types together, for example a mapping from an address to a struct, or a mapping to an array:

```solidity
struct Order {
    uint256 id;
    uint256 amount;
}

mapping(address => Order[]) public ordersByUser;
```

This pattern — mapping an address to a struct or array of structs — is one of the most common data-modeling patterns in Solidity, appearing throughout token contracts, marketplaces, and DAOs covered in later lessons.

[Previous](./[6]-Functions-and-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[8]-How-Blocks-and-Chains-Work.md)
