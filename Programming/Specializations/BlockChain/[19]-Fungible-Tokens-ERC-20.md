[Previous](./[18]-Upgradeable-Contracts-and-Proxies.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[20]-Non-Fungible-Tokens-ERC-721-and-ERC-1155.md)

*Token Standards*

# Lesson 19 - Fungible Tokens (ERC-20)

## 19.1 What Makes a Token "Fungible"

Fungible means interchangeable — one unit is identical in value and function to any other unit, the same way one dollar bill is interchangeable with any other dollar bill. ERC-20 is the Ethereum standard interface for fungible tokens, used for currencies, governance tokens, staking tokens, and countless other use cases.

---

## 19.2 The ERC-20 Interface

Every ERC-20 token implements a standard set of functions and events so that wallets, exchanges, and other contracts can interact with any ERC-20 token the same way:

```solidity
interface IERC20 {
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address to, uint256 amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint256);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address from, address to, uint256 amount) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
}
```

---

## 19.3 Approve and TransferFrom

`approve` lets a token holder authorize another address (`spender`) to move up to a set amount of their tokens, and `transferFrom` is what the spender then calls to actually move them. This two-step "allowance" pattern is what lets decentralized exchanges and other contracts move a user's tokens on their behalf, only up to an amount the user has explicitly permitted.

---

## 19.4 Using OpenZeppelin's Implementation

Rather than writing an ERC-20 token from scratch, most developers extend OpenZeppelin's audited `ERC20` contract:

```solidity
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor() ERC20("MyToken", "MTK") {
        _mint(msg.sender, 1_000_000 * 10 ** decimals());
    }
}
```

This gives a fully standards-compliant, security-reviewed token with just a few lines of custom code.

[Previous](./[18]-Upgradeable-Contracts-and-Proxies.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[20]-Non-Fungible-Tokens-ERC-721-and-ERC-1155.md)
