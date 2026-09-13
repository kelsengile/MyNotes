[Previous](./[4]-Indexes-And-Query-Planning.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[6]-Roles-Extensions-And-The-Ecosystem.md)

*Advanced PostgreSQL*

# Lesson 5 - Transactions And MVCC

## 5.1 Transactions In PostgreSQL

PostgreSQL is fully ACID-compliant, and every single statement runs inside an implicit transaction unless wrapped in an explicit one:

```sql
BEGIN;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;
-- or ROLLBACK;
```

---

## 5.2 MVCC Explained

PostgreSQL implements transactions using **Multi-Version Concurrency Control (MVCC)**. Instead of locking rows for every read, each transaction sees a consistent *snapshot* of the data as it existed when the transaction began. When a row is updated, PostgreSQL doesn't overwrite it in place — it writes a new version and marks the old one as outdated.

> 💡 **Analogy:** MVCC is like giving every reader their own photocopy of the page they're looking at, taken the moment they opened the book — even if someone else edits the real page a second later, the reader's copy doesn't change mid-read.

This is why readers in PostgreSQL almost never block writers, and vice versa — a major advantage for highly concurrent applications.

---

## 5.3 Isolation Levels

PostgreSQL supports the standard SQL isolation levels (see Database Fundamentals, Lesson 14):

| Level | Behavior |
|---|---|
| **Read Committed** (default) | Sees only data committed before each statement begins |
| **Repeatable Read** | Sees a consistent snapshot for the whole transaction |
| **Serializable** | Strictest — guarantees transactions behave as if run one at a time |

```sql
BEGIN ISOLATION LEVEL SERIALIZABLE;
```

---

## 5.4 VACUUM And Bloat

Because MVCC leaves old row versions behind instead of deleting them immediately, PostgreSQL needs a cleanup process called **VACUUM** to reclaim that space:

```sql
VACUUM products;
VACUUM ANALYZE;      -- also refreshes planner statistics
```

Without regular vacuuming (usually handled automatically by `autovacuum`), tables can suffer from **bloat** — wasted disk space and slower queries caused by too many dead row versions accumulating over time.

[Previous](./[4]-Indexes-And-Query-Planning.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[6]-Roles-Extensions-And-The-Ecosystem.md)