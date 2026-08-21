[Previous](./[36]-DAOs-and-Governance.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[38]-Local-Blockchain-Networks-and-Forking-Mainnet.md)

*Tooling & Testing*

# Lesson 37 - Testing Frameworks (Hardhat, Foundry, Truffle)

## 37.1 Hardhat Testing

Hardhat tests are typically written in JavaScript or TypeScript using Mocha and Chai, combined with Hardhat's Chai matchers for blockchain-specific assertions:

```javascript
const { expect } = require("chai");

describe("SimpleStorage", function () {
  it("stores and retrieves a value", async function () {
    const factory = await ethers.getContractFactory("SimpleStorage");
    const contract = await factory.deploy();
    await contract.set(42);
    expect(await contract.get()).to.equal(42);
  });
});
```

Run with `npx hardhat test`.

---

## 37.2 Foundry Testing

Foundry tests are written directly in Solidity, extending its `Test` base contract, which gives access to "cheatcodes" for manipulating blockchain state during tests (changing `msg.sender`, warping time, expecting reverts):

```solidity
import "forge-std/Test.sol";
import "../src/SimpleStorage.sol";

contract SimpleStorageTest is Test {
    SimpleStorage public simpleStorage;

    function setUp() public {
        simpleStorage = new SimpleStorage();
    }

    function testSetAndGet() public {
        simpleStorage.set(42);
        assertEq(simpleStorage.get(), 42);
    }
}
```

Run with `forge test`. Because tests are written in the same language as the contracts, Foundry tests tend to run significantly faster than Hardhat's JavaScript-based tests.

---

## 37.3 Truffle

Truffle was one of the earliest and most influential Ethereum development frameworks, offering compilation, testing, and deployment in a single tool. While Hardhat and Foundry have become more dominant in new projects, Truffle is still encountered in older or legacy codebases, and understanding it helps when maintaining or migrating such projects.

---

## 37.4 Choosing a Framework

| | Hardhat | Foundry |
|---|---|---|
| Test language | JavaScript/TypeScript | Solidity |
| Best for | Teams comfortable in JS, rich plugin ecosystem | Speed, native fuzzing, Solidity-only workflows |
| Local node | Hardhat Network | Anvil |

Many teams use both together — Foundry for fast unit/fuzz tests, Hardhat for complex deployment scripts and front-end integration testing.

[Previous](./[36]-DAOs-and-Governance.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[38]-Local-Blockchain-Networks-and-Forking-Mainnet.md)
