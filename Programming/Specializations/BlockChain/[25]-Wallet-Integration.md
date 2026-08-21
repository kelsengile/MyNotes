[Previous](./[24]-Building-a-Simple-dApp-UI.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[26]-Design-Patterns-in-Solidity.md)

*Decentralized Applications (dApps)*

# Lesson 25 - Wallet Integration (MetaMask, WalletConnect)

## 25.1 Detecting an Injected Wallet

Browser extension wallets like MetaMask inject a provider object (`window.ethereum`) into the page, which a dApp can detect and request access to:

```javascript
if (window.ethereum) {
  const accounts = await window.ethereum.request({ method: "eth_requestAccounts" });
}
```

This is the most common way desktop-browser dApps connect to a user's wallet.

---

## 25.2 WalletConnect

Not every user has a browser extension wallet — many prefer mobile wallet apps. **WalletConnect** is an open protocol that bridges a dApp (often running on desktop) with a mobile wallet app by generating a QR code or deep link; scanning it with the wallet establishes an encrypted session so the mobile app can approve connections and sign transactions remotely.

---

## 25.3 Handling Network and Account Changes

Wallets can change accounts or switch networks at any time without the dApp initiating it, so a robust integration listens for these events and updates its UI accordingly:

```javascript
window.ethereum.on("accountsChanged", (accounts) => { /* update UI */ });
window.ethereum.on("chainChanged", (chainId) => { /* update UI or reload */ });
```

Failing to handle these events is a common source of bugs where a dApp keeps showing stale account or network data after a user switches.

---

## 25.4 Multi-Wallet Support

Since users have a range of wallet preferences, many production dApps use a wallet-connection library (such as RainbowKit, Web3Modal, or ConnectKit) that provides a unified UI supporting multiple wallet types — injected extensions, WalletConnect, and embedded/social-login wallets — behind a single "Connect Wallet" button, rather than hand-coding support for each wallet individually.

[Previous](./[24]-Building-a-Simple-dApp-UI.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[26]-Design-Patterns-in-Solidity.md)
