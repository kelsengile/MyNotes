[Previous](./[43]-Smart-Contract-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[45]-Legal-and-Regulatory-Considerations.md)

*Best Practices*

# Lesson 44 - Gas & Cost Optimization Best Practices

## 44.1 Optimize for Users, Not Just Deployment

Gas optimization matters most for functions users call frequently, not just for the one-time cost of deployment. A slightly larger contract that's cheaper to *use* is usually a better trade-off than a smaller contract that's expensive on every call, since deployment cost is paid once but usage cost is paid repeatedly by every user.

---

## 44.2 Revisit the Fundamentals in Context

The techniques from Lesson 29 — minimizing storage writes, packing variables, using `calldata`, caching repeated reads — should be applied deliberately to the functions that matter most: the ones on a project's critical, high-traffic path (like a token transfer or a swap function), rather than spread evenly and indiscriminately across every function in a codebase.

---

## 44.3 Batch Operations

Where possible, designing functions that let users perform multiple actions in a single transaction (e.g. `batchTransfer`, `multicall`) saves gas by amortizing the fixed overhead cost every transaction pays, compared to sending several separate transactions for the same end result.

---

## 44.4 Don't Sacrifice Security for Gas

Aggressive gas optimization (such as removing safety checks, using `unchecked` blocks carelessly, or skipping input validation) can directly introduce vulnerabilities. Optimization should always be balanced against — and never come at the direct expense of — the security best practices covered in Lesson 43; a cheaper but exploitable contract is a worse outcome than a slightly more expensive but safe one.

---

## 44.5 Measure Before and After

Gas optimization should be data-driven: measuring actual gas costs before and after a change (using a gas reporter or snapshot tool, as covered in Lesson 29) confirms an optimization actually helped, and by how much, rather than relying on assumptions about what "should" be cheaper.

[Previous](./[43]-Smart-Contract-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-BlockChain.md) | [Next](./[45]-Legal-and-Regulatory-Considerations.md)
