[Previous](./[20]-Non-Fungible-Tokens-ERC-721-and-ERC-1155.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[22]-Connecting-a-Frontend-to-a-Smart-Contract.md)

*Token Standards*

# Lesson 21 - Token Minting & Burning

## 21.1 What Minting and Burning Mean

**Minting** creates new tokens and increases total supply; **burning** destroys tokens and decreases it. Both operations directly change a token's supply and are common building blocks in rewards systems, buy-back mechanisms, NFT drops, and deflationary tokenomics.

---

## 21.2 Minting in Practice

OpenZeppelin's base contracts expose internal `_mint` functions that custom contracts wrap with their own access control:

```solidity
function mint(address to, uint256 amount) public onlyOwner {
    _mint(to, amount);
}
```

For NFTs, minting typically assigns the next sequential token ID and, often, requires payment:

```solidity
function mintNFT() public payable {
    require(msg.value >= mintPrice, "Insufficient payment");
    uint256 tokenId = nextTokenId++;
    _safeMint(msg.sender, tokenId);
}
```

---

## 21.3 Burning in Practice

Burning removes a token from circulation, either by the holder themselves or under specific contract rules:

```solidity
function burn(uint256 amount) public {
    _burn(msg.sender, amount);
}
```

Some protocols burn tokens automatically as part of their mechanics — for example, transaction fees that are partially burned rather than fully redistributed, reducing supply over time.

---

## 21.4 Access Control Around Supply Changes

Because minting and burning directly affect a token's economics, these functions are almost always gated by access control (an `onlyOwner` modifier, a role-based system, or a DAO vote) to prevent unauthorized supply manipulation. A minting function with no access restriction is a critical vulnerability, since anyone could mint themselves unlimited tokens.

[Previous](./[20]-Non-Fungible-Tokens-ERC-721-and-ERC-1155.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[22]-Connecting-a-Frontend-to-a-Smart-Contract.md)
