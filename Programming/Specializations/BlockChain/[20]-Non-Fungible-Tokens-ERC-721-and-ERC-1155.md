[Previous](./[19]-Fungible-Tokens-ERC-20.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[21]-Token-Minting-and-Burning.md)

*Token Standards*

# Lesson 20 - Non-Fungible Tokens (ERC-721, ERC-1155)

## 20.1 What Makes a Token "Non-Fungible"

Unlike ERC-20 tokens, non-fungible tokens (NFTs) represent unique, individually distinguishable assets — digital art, collectibles, game items, real-world asset ownership records — where each token ID has its own identity and, often, its own metadata.

---

## 20.2 The ERC-721 Standard

ERC-721 tracks ownership of individual token IDs rather than a fungible balance:

```solidity
interface IERC721 {
    function ownerOf(uint256 tokenId) external view returns (address);
    function balanceOf(address owner) external view returns (uint256);
    function transferFrom(address from, address to, uint256 tokenId) external;
    function approve(address to, uint256 tokenId) external;
    event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
}
```

Each token ID typically points to a **metadata URI** (often pointing to a JSON file on IPFS, see Lesson 30) describing the asset's name, image, and attributes — the metadata itself usually isn't stored on-chain due to storage cost.

---

## 20.3 The ERC-1155 Multi-Token Standard

ERC-1155 lets a single contract manage many token types — fungible, non-fungible, or semi-fungible — under one contract, each identified by a token ID with its own balance per owner. This is especially popular in gaming, where a single contract might manage thousands of unique item types alongside fungible in-game currency, and lets a single transaction batch-transfer multiple token types at once, saving gas compared to deploying and calling many separate contracts.

---

## 20.4 Choosing Between Them

| | ERC-721 | ERC-1155 |
|---|---|---|
| Best for | Truly unique, one-of-a-kind assets | Mixed collections, game items, editions |
| Balance model | 1 owner per token ID | Balance per address per token ID |
| Batch operations | Not natively supported | Built in (`safeBatchTransferFrom`) |
| Gas efficiency (many items) | Lower | Higher |

Both standards typically build on OpenZeppelin's audited `ERC721` and `ERC1155` base contracts rather than being implemented from scratch.

[Previous](./[19]-Fungible-Tokens-ERC-20.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[21]-Token-Minting-and-Burning.md)
