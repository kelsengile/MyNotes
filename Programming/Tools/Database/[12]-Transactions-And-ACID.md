[Previous](./[11]-Joins-And-Subqueries.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[13]-Indexing-And-Query-Performance.md)

*Transactions And Performance*

# Lesson 12 - Transactions And ACID

## 12.1 What Is a Transaction

A **transaction** groups one or more database operations into a single unit of work that either fully succeeds or fully fails — there's no in-between state. The classic example is transferring money between two bank accounts: the debit from one account and the credit to the other must happen together, or not at all. If the debit succeeded but the credit failed, money would simply vanish.

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

---

## 12.2 Atomicity

**Atomicity** is the "all or nothing" guarantee: every operation inside a transaction either completes together, or none of them take effect. If any statement in the transaction fails, the whole transaction is **rolled back**, undoing any partial changes as if nothing happened.

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
-- something goes wrong here
ROLLBACK;
```

---

## 12.3 Consistency, Isolation, and Durability

Atomicity is one of four guarantees, together known as **ACID**:

- **Consistency** — a transaction can only bring the database from one valid state to another, never violating defined rules like constraints (see [5.3](./[5]-Schemas-And-Data-Types.md)).
- **Isolation** — concurrent transactions don't interfere with each other; each one behaves as if it were running alone (explored further in [Lesson 14](./[14]-Concurrency-And-Locking.md)).
- **Durability** — once a transaction is committed, it stays committed, even if the system crashes immediately afterward.

Together, ACID is the foundation that makes relational databases trustworthy for critical data like financial records.

---

## 12.4 Transaction Syntax Example

Most SQL databases use three key statements to control transactions:

- `BEGIN` (or `START TRANSACTION`) — starts a new transaction.
- `COMMIT` — makes all changes in the transaction permanent.
- `ROLLBACK` — discards all changes made since `BEGIN`.

```sql
BEGIN;

INSERT INTO orders (user_id, total) VALUES (1, 49.99);
UPDATE inventory SET stock = stock - 1 WHERE product_id = 7;

COMMIT;
```

If the inventory update failed because stock was already at zero, the application could issue `ROLLBACK` instead, ensuring the order is never created without matching inventory being reserved.

[Previous](./[11]-Joins-And-Subqueries.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[13]-Indexing-And-Query-Performance.md)
