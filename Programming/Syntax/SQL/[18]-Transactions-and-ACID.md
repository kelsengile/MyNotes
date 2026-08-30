[Previous](./[17]-Indexes-and-Performance.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[19]-CTEs-and-Recursive-Queries.md)

# Lesson 18 - Transactions & ACID Properties

---

## 18.1 What a transaction is

A **transaction** groups multiple SQL statements into a single, all-or-nothing unit of work. Either every statement in the transaction succeeds and is saved, or (if anything goes wrong) none of them are — the database is left exactly as if the transaction never happened.

---

## 18.2 The classic example: transferring money

```sql
BEGIN;

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

COMMIT;
```

If the database crashed *between* the two `UPDATE` statements without transactions, account 1 would lose $100 that account 2 never received — money would simply vanish. Wrapping both statements in a transaction guarantees that either both happen, or neither does.

---

## 18.3 BEGIN, COMMIT, ROLLBACK

```sql
BEGIN;                          -- start a transaction (some dialects use START TRANSACTION)
UPDATE books SET price = price * 1.1 WHERE genre = 'Fantasy';
-- check the results look right, e.g. with a SELECT...
COMMIT;                         -- make it permanent
```

If something looks wrong, undo everything since `BEGIN`:
```sql
BEGIN;
UPDATE books SET price = price * 1.1 WHERE genre = 'Fantasy';
ROLLBACK;                       -- undo — as if nothing happened
```

**Dialect note:** PostgreSQL and SQLite use `BEGIN`; MySQL and SQL Server typically use `START TRANSACTION` (MySQL also accepts `BEGIN`). SQL Server uses `COMMIT TRANSACTION` / `ROLLBACK TRANSACTION` in full form.

---

## 18.4 Autocommit mode

By default, most databases run in **autocommit** mode — every individual statement is its own implicit transaction, committed immediately. Explicit `BEGIN`/`COMMIT` blocks let you group several statements together instead.

---

## 18.5 The ACID properties

ACID is an acronym describing the guarantees a properly implemented transactional database provides:

### Atomicity
A transaction is all-or-nothing — partial completion is impossible. If any statement fails partway through, the entire transaction is rolled back automatically.

### Consistency
A transaction takes the database from one valid state to another, never violating constraints (Lesson 14), foreign keys (Lesson 13), or other rules along the way — a transaction that would leave the data in a rule-violating state is rejected entirely.

### Isolation
Concurrent transactions don't interfere with each other's intermediate, uncommitted state — as if each transaction ran alone, even when many are actually running at the same time. (Isolation *levels*, below, control exactly how strict this guarantee is.)

### Durability
Once a transaction is committed, it's permanent — it survives a crash, a power failure, or a restart immediately afterward, typically because it's been written to disk, not just held in memory.

---

## 18.6 Isolation levels

Full isolation between every concurrent transaction is expensive to guarantee perfectly, so SQL defines several isolation levels, trading strictness for performance:

| Level | Prevents |
|---|---|
| Read Uncommitted | Almost nothing — can see other transactions' uncommitted changes ("dirty reads") |
| Read Committed | Dirty reads (the default in PostgreSQL, SQL Server, Oracle) |
| Repeatable Read | Dirty reads + non-repeatable reads (the default in MySQL/InnoDB) |
| Serializable | Everything — transactions behave as if run one at a time, sequentially |

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```
Higher isolation levels give stronger guarantees but reduce how much work can happen concurrently, since the database must do more to prevent transactions from stepping on each other.

### Common anomalies these levels guard against

- **Dirty read**: reading another transaction's uncommitted (possibly soon-to-be-rolled-back) changes
- **Non-repeatable read**: reading the same row twice in one transaction and getting different values, because another transaction committed a change in between
- **Phantom read**: re-running the same query twice in one transaction and getting a *different set of rows*, because another transaction inserted or deleted matching rows in between

---

## 18.7 SAVEPOINT: partial rollback within a transaction

```sql
BEGIN;
UPDATE books SET price = price * 1.1 WHERE genre = 'Fantasy';
SAVEPOINT after_fantasy;

UPDATE books SET price = price * 1.1 WHERE genre = 'Science Fiction';
-- Oops, that one was wrong:
ROLLBACK TO SAVEPOINT after_fantasy;

COMMIT;   -- keeps the Fantasy price change, discards the Sci-Fi one
```

---

## 18.8 Deadlocks

When two transactions each hold a lock the other needs, they can wait on each other forever — a **deadlock**. Most databases detect this automatically and abort one of the transactions (raising an error) so the other can proceed. Application code that uses transactions should generally be prepared to catch this error and retry.

---

## 18.9 When to use explicit transactions

- Any time multiple statements must succeed or fail together (like the account transfer example)
- Before running a risky `UPDATE`/`DELETE` you want to double-check with a `SELECT` before committing
- Batch operations where partial completion would leave data inconsistent

---

[Previous](./[17]-Indexes-and-Performance.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[19]-CTEs-and-Recursive-Queries.md)
