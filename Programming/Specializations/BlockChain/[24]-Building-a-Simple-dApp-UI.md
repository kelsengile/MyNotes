[Previous](./[23]-Reading-and-Writing-On-Chain-Data.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[25]-Wallet-Integration.md)

*Decentralized Applications (dApps)*

# Lesson 24 - Building a Simple dApp UI

## 24.1 What Makes a UI "Decentralized"

A dApp's UI is usually a fairly ordinary web front-end (React, Vue, or similar) — what makes it "decentralized" is that instead of talking to a private company database, it reads and writes through a wallet directly to a public blockchain. The UI itself can even be hosted anywhere (including decentralized hosting like IPFS), since the actual state and logic live on-chain.

---

## 24.2 Core UI States to Handle

Blockchain UIs need to account for states that traditional web apps rarely deal with:

- **No wallet connected** — prompt the user to connect.
- **Wrong network** — the connected wallet is on a different chain than the dApp expects.
- **Pending transaction** — a transaction has been submitted but not yet confirmed.
- **Transaction failed/reverted** — the call failed and needs a clear error message.
- **Stale data** — on-chain state can change from other users' actions between page loads, so many dApps poll or subscribe to updates.

---

## 24.3 A Minimal Example Flow

```javascript
function MintButton({ contract }) {
  const [status, setStatus] = useState("idle");

  async function handleMint() {
    try {
      setStatus("pending");
      const tx = await contract.mintNFT({ value: mintPrice });
      await tx.wait();
      setStatus("success");
    } catch (err) {
      setStatus("error");
    }
  }

  return <button onClick={handleMint}>{status === "pending" ? "Minting..." : "Mint"}</button>;
}
```

---

## 24.4 UX Considerations

Because blockchain transactions are slower and costlier than typical API calls, good dApp UX sets clear expectations: showing estimated gas costs before a transaction is sent, giving clear pending/success/failure feedback, and linking to a block explorer so users can independently verify their transaction. Silent failures or unclear pending states are one of the most common sources of user frustration in dApps.

[Previous](./[23]-Reading-and-Writing-On-Chain-Data.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[25]-Wallet-Integration.md)
