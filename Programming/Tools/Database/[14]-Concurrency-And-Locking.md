[Previous](./[13]-Indexing-And-Query-Performance.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[15]-NoSQL-Database-Types.md)

*Transactions And Performance*

# Lesson 14 - Concurrency And Locking

## 14.1 The Concurrency Problem

Real databases are almost never used by one person at a time — many transactions (see [12.1](./[12]-Transactions-And-ACID.md)) run **concurrently**, often touching the same rows. Without any coordination, this can cause problems such as:

- **Lost updates** — two transactions read the same row, both calculate a new value, and one overwrites the other's change.
- **Dirty reads** — a transaction reads data that another transaction has changed but not yet committed, and that change is later rolled back.
- **Non-repeatable reads** — a transaction reads the same row twice and gets two different results because another transaction changed it in between.

For example, if two people try to book the last seat on a flight at the same instant, the database needs a way to make sure only one of them succeeds.

---

## 14.2 Locking Strategies

A **lock** prevents other transactions from modifying (or sometimes even reading) data that another transaction is currently working with. Databases use locking to enforce the **Isolation** guarantee from ACID (see [12.3](./[12]-Transactions-And-ACID.md)).

Common lock types:

- **Shared lock (read lock)** — multiple transactions can hold this on the same row at once, allowing concurrent reads.
- **Exclusive lock (write lock)** — only one transaction can hold this on a row at a time, blocking others from reading or writing it until it's released.

```sql
BEGIN;
SELECT * FROM seats WHERE id = 42 FOR UPDATE;
-- this row is now locked until the transaction commits or rolls back
UPDATE seats SET booked = true WHERE id = 42;
COMMIT;
```

`FOR UPDATE` explicitly requests an exclusive lock on the selected row, so no other transaction can book the same seat until this one finishes.

---

## 14.3 Isolation Levels

Locking every row for every read would make a database very slow, so most systems let you choose an **isolation level** — a trade-off between consistency and performance:

| Isolation Level | Prevents | Allows |
|---|---|---|
| Read Uncommitted | Nothing | Dirty reads |
| Read Committed | Dirty reads | Non-repeatable reads |
| Repeatable Read | Dirty reads, non-repeatable reads | Phantom reads |
| Serializable | All of the above | Transactions behave as if run one at a time |

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

Stricter isolation levels give stronger guarantees but reduce how many transactions can run truly in parallel, which can hurt performance under heavy load.

---

## 14.4 Deadlocks

A **deadlock** happens when two transactions each hold a lock the other one needs, and both end up waiting forever.

```sql
-- Transaction A
BEGIN;
UPDATE accounts SET balance = balance - 50 WHERE id = 1; -- locks row 1
UPDATE accounts SET balance = balance + 50 WHERE id = 2; -- waits for row 2

-- Transaction B (running at the same time)
BEGIN;
UPDATE accounts SET balance = balance - 50 WHERE id = 2; -- locks row 2
UPDATE accounts SET balance = balance + 50 WHERE id = 1; -- waits for row 1
```

Here, Transaction A is waiting on the lock Transaction B holds, and Transaction B is waiting on the lock Transaction A holds — neither can proceed. Most databases detect this automatically and resolve it by forcibly rolling back one of the transactions, letting the other continue.

Application code that runs transactions should always be prepared to catch a deadlock error and retry the transaction, since deadlocks are a normal (if occasional) part of concurrent systems rather than a sign of a bug.

[Previous](./[13]-Indexing-And-Query-Performance.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[15]-NoSQL-Database-Types.md)
