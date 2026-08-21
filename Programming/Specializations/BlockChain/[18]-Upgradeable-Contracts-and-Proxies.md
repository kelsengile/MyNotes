[Previous](./[17]-Error-Handling-and-Require-Revert.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[19]-Fungible-Tokens-ERC-20.md)

*Smart Contracts*

# Lesson 18 - Upgradeable Contracts & Proxies

## 18.1 The Immutability Problem

Once deployed, a contract's code cannot be changed — this is a core security guarantee, but it also means bugs discovered after launch can't simply be patched. Upgradeability patterns exist to let teams fix bugs or add features while preserving the contract's address and existing on-chain state (like user balances).

---

## 18.2 The Proxy Pattern

The most common solution separates a contract into two pieces: a **proxy** contract, which users actually interact with and which holds all the state, and a **logic (implementation)** contract, which holds the code but no meaningful state of its own. The proxy uses `delegatecall` to execute the logic contract's code in the proxy's own storage context, so upgrading means simply pointing the proxy at a new logic contract address.

---

## 18.3 delegatecall

`delegatecall` is a low-level EVM call that runs another contract's code as if it were the calling contract's own code — meaning it reads and writes to the *caller's* storage, not its own. This is what makes proxies possible, but it also means storage layouts between the proxy and each logic version must stay carefully aligned, since mismatches can corrupt contract state.

---

## 18.4 Common Proxy Standards

- **Transparent Proxy (EIP-1967)** — separates admin and user calls to avoid function-selector clashes.
- **UUPS (EIP-1822)** — puts the upgrade logic in the implementation contract itself, saving gas on deployment.
- **Beacon Proxy** — many proxies point to one shared "beacon" contract, allowing a single upgrade to affect many proxy instances at once.

OpenZeppelin's Upgrades plugins are the standard tooling for safely deploying and upgrading these patterns, and they include checks that catch unsafe storage-layout changes before an upgrade is applied.

---

## 18.5 Trade-offs

Upgradeability adds flexibility but also risk: if the account controlling upgrades is compromised, an attacker could redirect the proxy to malicious logic and drain user funds. Many production protocols mitigate this with a multi-signature wallet or a timelock (a mandatory delay before an upgrade takes effect) controlling the upgrade process.

[Previous](./[17]-Error-Handling-and-Require-Revert.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[19]-Fungible-Tokens-ERC-20.md)
