[⬅ Back to README](../../README.md)

# Blockchain Development

Welcome! This is a self-paced course for learning Blockchain Development, the practice of building decentralized systems, smart contracts, and applications on top of distributed ledgers.

---

## What is Blockchain Development?

Blockchain Development lets you:
- Understand how blocks, chains, and consensus mechanisms keep a ledger secure and decentralized
- Write, test, and deploy smart contracts (Solidity and beyond)
- Create and manage tokens (ERC-20, ERC-721, ERC-1155)
- Build decentralized applications (dApps) that read and write on-chain data
- Connect wallets and front-ends to smart contracts with Web3/Ethers libraries
- Work with decentralized storage, oracles, and cross-chain bridges
- Understand DeFi primitives like exchanges, AMMs, and DAOs
- Audit, test, and optimize contracts for security and gas efficiency
- Deploy to testnets and mainnet, and monitor on-chain activity in production

## Table of Contents

**Getting Started**  
    1. **[What is Blockchain? Core Concepts & History](./[1]-What-is-Blockchain.md)**  
       1.1 What is a Blockchain?  
       1.2 Decentralization vs. Centralization  
       1.3 A Brief History  
       1.4 Core Properties  
    2. **[Development Environment & Toolchains (Node, Hardhat/Foundry, Wallets)](./%5B2%5D-Development-Environment%20%283%29.md)**  
       2.1 Node.js and Package Managers  
       2.2 Development Frameworks: Hardhat and Foundry  
       2.3 Wallets for Development  
       2.4 Editors and Extensions  
       2.5 Setting Up a Project  
    3. **[Anatomy of a Blockchain Project](./[3]-Anatomy-of-a-Blockchain-Project.md)**  
       3.1 Typical Folder Structure  
       3.2 The Compilation Pipeline  
       3.3 Front-End and Off-Chain Components  
       3.4 Dependencies and Libraries  

**Core Syntax**  
    4. **[Variables, Data Types & Operators](./%5B4%5D-Variables-and-Data-Types%20%281%29.md)**  
       4.1 Value Types  
       4.2 Reference Types  
       4.3 State Variables vs. Local Variables  
       4.4 Operators  
    5. **[Control Flow: Conditionals & Loops](./%5B5%5D-Control-Flow%20%281%29.md)**  
       5.1 Conditionals  
       5.2 Loops  
       5.3 Gas-Aware Control Flow  
       5.4 Short-Circuiting and Early Returns  
    6. **[Functions & Modifiers](./[6]-Functions-and-Modifiers.md)**  
       6.1 Function Anatomy  
       6.2 Visibility  
       6.3 State Mutability  
       6.4 Modifiers  
       6.5 Function Overloading and Return Values  
    7. **[Structs, Arrays & Mappings](./[7]-Structs-Arrays-and-Mappings.md)**  
       7.1 Structs  
       7.2 Arrays  
       7.3 Mappings  
       7.4 Combining Them  

**Blockchain Fundamentals**  
    8. **[How Blocks & Chains Work](./[8]-How-Blocks-and-Chains-Work.md)**  
       8.1 What's Inside a Block  
       8.2 Linking Blocks Together  
       8.3 Merkle Trees  
       8.4 The Genesis Block and Chain Growth  
    9. **[Consensus Mechanisms (PoW, PoS)](./[9]-Consensus-Mechanisms.md)**  
       9.1 Why Consensus Is Needed  
       9.2 Proof of Work (PoW)  
       9.3 Proof of Stake (PoS)  
       9.4 Comparing the Two  
    10. **[Cryptographic Hashing & Digital Signatures](./[10]-Cryptographic-Hashing-and-Digital-Signatures.md)**  
        10.1 Hash Functions  
        10.2 Public-Key Cryptography  
        10.3 Digital Signatures  
        10.4 Why This Matters for Security  
    11. **[Wallets, Keys & Addresses](./[11]-Wallets-Keys-and-Addresses.md)**  
        11.1 From Private Key to Address  
        11.2 Seed Phrases and HD Wallets  
        11.3 Wallet Types  
        11.4 Externally Owned Accounts vs. Contract Accounts  
    12. **[Transactions & Gas](./[12]-Transactions-and-Gas.md)**  
        12.1 Anatomy of a Transaction  
        12.2 What Is Gas?  
        12.3 EIP-1559 and Fee Structure  
        12.4 Transaction Lifecycle  

**Smart Contracts**  
    13. **[Introduction to Smart Contracts](./[13]-Introduction-to-Smart-Contracts.md)**  
        13.1 What Is a Smart Contract?  
        13.2 The Ethereum Virtual Machine (EVM)  
        13.3 Contract Deployment and Addresses  
        13.4 What Smart Contracts Enable  
    14. **[Writing Your First Smart Contract (Solidity)](./[14]-Writing-Your-First-Smart-Contract.md)**  
        14.1 Contract Skeleton  
        14.2 The Constructor  
        14.3 Global Variables  
        14.4 Compiling and Running Locally  
    15. **[Deploying Contracts to a Testnet](./[15]-Deploying-Contracts-to-a-Testnet.md)**  
        15.1 What Are Testnets?  
        15.2 Getting Test ETH  
        15.3 Connecting to a Testnet  
        15.4 Running a Deployment  
    16. **[Contract Events & Logging](./[16]-Contract-Events-and-Logging.md)**  
        16.1 Why Events Exist  
        16.2 Declaring and Emitting Events  
        16.3 Indexed Parameters  
        16.4 Listening for Events Off-Chain  
    17. **[Error Handling & Require/Revert](./[17]-Error-Handling-and-Require-Revert.md)**  
        17.1 require  
        17.2 revert and Custom Errors  
        17.3 assert  
        17.4 try/catch  
    18. **[Upgradeable Contracts & Proxies](./[18]-Upgradeable-Contracts-and-Proxies.md)**  
        18.1 The Immutability Problem  
        18.2 The Proxy Pattern  
        18.3 delegatecall  
        18.4 Common Proxy Standards  
        18.5 Trade-offs  

**Token Standards**  
    19. **[Fungible Tokens (ERC-20)](./[19]-Fungible-Tokens-ERC-20.md)**  
        19.1 What Makes a Token "Fungible"  
        19.2 The ERC-20 Interface  
        19.3 Approve and TransferFrom  
        19.4 Using OpenZeppelin's Implementation  
    20. **[Non-Fungible Tokens (ERC-721, ERC-1155)](./[20]-Non-Fungible-Tokens-ERC-721-and-ERC-1155.md)**  
        20.1 What Makes a Token "Non-Fungible"  
        20.2 The ERC-721 Standard  
        20.3 The ERC-1155 Multi-Token Standard  
        20.4 Choosing Between Them  
    21. **[Token Minting & Burning](./[21]-Token-Minting-and-Burning.md)**  
        21.1 What Minting and Burning Mean  
        21.2 Minting in Practice  
        21.3 Burning in Practice  
        21.4 Access Control Around Supply Changes  

**Decentralized Applications (dApps)**  
    22. **[Connecting a Front-End to a Smart Contract (Web3.js/Ethers.js)](./[22]-Connecting-a-Frontend-to-a-Smart-Contract.md)**  
        22.1 What a Web3 Library Does  
        22.2 Providers and Signers  
        22.3 Instantiating a Contract  
        22.4 Read vs. Write Calls  
    23. **[Reading & Writing On-Chain Data](./[23]-Reading-and-Writing-On-Chain-Data.md)**  
        23.1 Reading Current State  
        23.2 Reading Historical Data with Events  
        23.3 Writing Data (Sending Transactions)  
        23.4 Handling Confirmation and Finality  
    24. **[Building a Simple dApp UI](./[24]-Building-a-Simple-dApp-UI.md)**  
        24.1 What Makes a UI "Decentralized"  
        24.2 Core UI States to Handle  
        24.3 A Minimal Example Flow  
        24.4 UX Considerations  
    25. **[Wallet Integration (MetaMask, WalletConnect)](./[25]-Wallet-Integration.md)**  
        25.1 Detecting an Injected Wallet  
        25.2 WalletConnect  
        25.3 Handling Network and Account Changes  
        25.4 Multi-Wallet Support  

**Advanced Smart Contract Development**  
    26. **[Design Patterns in Solidity](./[26]-Design-Patterns-in-Solidity.md)**  
        26.1 Checks-Effects-Interactions  
        26.2 Access Control Patterns  
        26.3 Pull over Push Payments  
        26.4 Factory Pattern  
    27. **[Security Vulnerabilities & Common Exploits (Reentrancy, Overflow)](./[27]-Security-Vulnerabilities-and-Common-Exploits.md)**  
        27.1 Reentrancy  
        27.2 Integer Overflow/Underflow  
        27.3 Access Control Failures  
        27.4 Price Oracle Manipulation  
        27.5 Front-Running  
    28. **[Auditing & Testing Smart Contracts](./[28]-Auditing-and-Testing-Smart-Contracts.md)**  
        28.1 Why Testing Is Non-Negotiable  
        28.2 Unit and Integration Testing  
        28.3 Fuzzing and Property-Based Testing  
        28.4 Static Analysis  
        28.5 Professional Audits  
    29. **[Gas Optimization Techniques](./[29]-Gas-Optimization-Techniques.md)**  
        29.1 Storage Is Expensive  
        29.2 Variable Packing  
        29.3 Using calldata and immutable/constant  
        29.4 Short-Circuiting and Caching  
        29.5 Measuring Gas Usage  

**Decentralized Systems**  
    30. **[Decentralized Storage (IPFS)](./[30]-Decentralized-Storage-IPFS.md)**  
        30.1 Why Not Store Everything On-Chain?  
        30.2 What Is IPFS?  
        30.3 How Content Addressing Ties into Smart Contracts  
        30.4 Pinning and Persistence  
        30.5 Other Decentralized Storage Options  
    31. **[Oracles & Off-Chain Data (Chainlink)](./[31]-Oracles-and-Off-Chain-Data.md)**  
        31.1 The Oracle Problem  
        31.2 Decentralized Oracle Networks  
        31.3 Common Oracle Use Cases  
        31.4 Risks of Oracle Dependence  
    32. **[Layer 2 Solutions & Scaling](./[32]-Layer-2-Solutions-and-Scaling.md)**  
        32.1 The Scalability Problem  
        32.2 What Are Layer 2s?  
        32.3 Rollups  
        32.4 Sidechains and Other Scaling Approaches  
        32.5 Trade-offs  
    33. **[Cross-Chain Bridges & Interoperability](./[33]-Cross-Chain-Bridges-and-Interoperability.md)**  
        33.1 Why Bridges Exist  
        33.2 Lock-and-Mint Bridges  
        33.3 Bridge Security Models  
        33.4 Interoperability Protocols  

**DeFi & Advanced Concepts**  
    34. **[Decentralized Finance (DeFi) Basics](./[34]-Decentralized-Finance-Basics.md)**  
        34.1 What Is DeFi?  
        34.2 Lending and Borrowing  
        34.3 Stablecoins  
        34.4 Yield Farming and Liquidity Mining  
    35. **[Decentralized Exchanges & AMMs](./[35]-Decentralized-Exchanges-and-AMMs.md)**  
        35.1 Order Books vs. Automated Market Makers  
        35.2 The Constant Product Formula  
        35.3 Liquidity Providers  
        35.4 Slippage and Price Impact  
    36. **[DAOs & Governance](./[36]-DAOs-and-Governance.md)**  
        36.1 What Is a DAO?  
        36.2 Governance Tokens and Voting  
        36.3 The Proposal Lifecycle  
        36.4 Challenges in DAO Governance  

**Tooling & Testing**  
    37. **[Testing Frameworks (Hardhat, Foundry, Truffle)](./[37]-Testing-Frameworks.md)**  
        37.1 Hardhat Testing  
        37.2 Foundry Testing  
        37.3 Truffle  
        37.4 Choosing a Framework  
    38. **[Local Blockchain Networks & Forking Mainnet](./[38]-Local-Blockchain-Networks-and-Forking-Mainnet.md)**  
        38.1 Why Use a Local Network?  
        38.2 Useful Local Network Features  
        38.3 Forking Mainnet  
        38.4 When Forking Is Essential  
    39. **[Version Control for Smart Contract Projects](./[39]-Version-Control-for-Smart-Contract-Projects.md)**  
        39.1 Why Version Control Matters More Here  
        39.2 What to Exclude from Git  
        39.3 Branching and Review for Contract Changes  
        39.4 Tracking Deployed Addresses  

**Deployment & Production**  
    40. **[Deploying to Mainnet](./[40]-Deploying-to-Mainnet.md)**  
        40.1 Pre-Deployment Checklist  
        40.2 The Cost of Mainnet Deployment  
        40.3 Using a Secure Deployer Key  
        40.4 Post-Deployment Steps  
    41. **[Contract Verification & Documentation](./[41]-Contract-Verification-and-Documentation.md)**  
        41.1 What Contract Verification Means  
        41.2 Why Verification Matters  
        41.3 Automating Verification  
        41.4 NatSpec Documentation  
    42. **[Monitoring On-Chain Activity](./[42]-Monitoring-On-Chain-Activity.md)**  
        42.1 Why Monitoring Doesn't Stop at Deployment  
        42.2 Event-Based Monitoring  
        42.3 Monitoring Tools and Services  
        42.4 Building In Emergency Response  

**Best Practices**  
    43. **[Smart Contract Security Best Practices](./[43]-Smart-Contract-Security-Best-Practices.md)**  
        43.1 Defense in Depth  
        43.2 Minimize Trust and Attack Surface  
        43.3 Use Audited, Standard Libraries  
        43.4 Plan for Failure  
        43.5 Stay Current  
    44. **[Gas & Cost Optimization Best Practices](./[44]-Gas-and-Cost-Optimization-Best-Practices.md)**  
        44.1 Optimize for Users, Not Just Deployment  
        44.2 Revisit the Fundamentals in Context  
        44.3 Batch Operations  
        44.4 Don't Sacrifice Security for Gas  
        44.5 Measure Before and After  
    45. **[Legal & Regulatory Considerations](./[45]-Legal-and-Regulatory-Considerations.md)**  
        45.1 A Fast-Moving and Fragmented Landscape  
        45.2 Securities Law Questions  
        45.3 KYC/AML Considerations  
        45.4 Tax Implications  
        45.5 Building Responsibly  
    46. **[Blockchain Career Paths & Ecosystem Overview](./[46]-Blockchain-Career-Paths-and-Ecosystem-Overview.md)**
        46.1 Smart Contract / Solidity Developer  
        46.2 Smart Contract Security / Auditor  
        46.3 Protocol / Research Engineer  
        46.4 Front-End / dApp Developer  
        46.5 Other Roles in the Ecosystem  
        46.6 Continuing to Learn  