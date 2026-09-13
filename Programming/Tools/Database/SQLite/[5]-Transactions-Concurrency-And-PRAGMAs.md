[Previous](./[4]-Indexes-And-Query-Planning-In-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[6]-Backups-Extensions-And-The-SQLite-Ecosystem.md)

*SQLite In Production*

# Lesson 5 - Transactions, Concurrency, And PRAGMAs

## 5.1 Transactions In SQLite

SQLite is fully **ACID-compliant** (see Database Fundamentals, [Lesson 12](../[12]-Transactions-And-ACID.md)), grouping statements so they either all succeed or all fail together:

```sql
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;
-- or ROLLBACK; to undo both statements
```

Like MySQL, SQLite defaults to **autocommit** — every individual statement is its own transaction unless wrapped in an explicit `BEGIN` ... `COMMIT` block.

---

## 5.2 Locking And The Single-Writer Model

Because SQLite has no separate server process coordinating access, concurrency is handled through **file locks** on the database file itself, with a simple rule: **many readers, or one writer, at a time.**

- Multiple connections (even from different processes) can read the database simultaneously.
- Only one connection can write at any given moment; other writers must wait.
- A writer blocked by another writer for too long raises a `SQLITE_BUSY` error, which applications typically handle by retrying after a short delay.

> 💡 **Analogy:** Think of the database file as a single shared whiteboard. Any number of people can look at it at once (readers), but only one person can hold the marker and write on it at a time (the single writer) — everyone else has to wait their turn.

This model is what makes SQLite unsuitable for applications with many concurrent writers (see [6.4](./[6]-Backups-Extensions-And-The-SQLite-Ecosystem.md)), but it's rarely a problem for the embedded, mostly single-application workloads SQLite is designed for.

---

## 5.3 WAL Mode

By default, SQLite uses a **rollback journal**, which briefly blocks readers while a write is in progress. Switching to **Write-Ahead Logging (WAL)** mode changes this so that readers and a writer can operate at the same time:

```sql
PRAGMA journal_mode = WAL;
```

In WAL mode, writes are first appended to a separate `-wal` file rather than modifying the main database file directly; readers keep working from a consistent snapshot while the write is pending, and the WAL file is periodically merged back ("checkpointed") into the main file. WAL mode is a common recommendation for any application with more than very light concurrent access.

---

## 5.4 Useful PRAGMAs

**PRAGMAs** are SQLite's mechanism for querying or changing engine-level settings — there's no separate configuration file or server restart involved, since they take effect per-connection or per-database:

```sql
PRAGMA foreign_keys = ON;     -- enforce foreign key constraints (off by default!)
PRAGMA journal_mode;          -- check the current journal mode
PRAGMA table_info(products);  -- list columns, types, and constraints for a table
PRAGMA synchronous = NORMAL;  -- trade some durability for write speed
```

> 💡 One of the most commonly missed SQLite quirks: foreign key constraints are **not enforced by default** and must be turned on with `PRAGMA foreign_keys = ON;` at the start of every connection, unlike MySQL and PostgreSQL where they're always active.

[Previous](./[4]-Indexes-And-Query-Planning-In-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[6]-Backups-Extensions-And-The-SQLite-Ecosystem.md)
