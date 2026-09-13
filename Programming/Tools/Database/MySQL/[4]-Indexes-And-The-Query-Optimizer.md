[Previous](./[3]-CRUD-And-Querying-In-MySQL.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[5]-Transactions-Users-And-Security.md)

*Performance And Administration*

# Lesson 4 - Indexes And The Query Optimizer

## 4.1 Index Types In MySQL

MySQL's default InnoDB engine primarily uses **B-tree indexes**, the same general structure covered in Database Fundamentals, plus a few specialized types:

| Index Type | Purpose |
|---|---|
| **PRIMARY** | Automatically created on the primary key; the table's data is physically stored in this order (a "clustered index") |
| **UNIQUE** | Enforces uniqueness while also speeding up lookups |
| **INDEX (regular)** | Speeds up `WHERE`/`JOIN`/`ORDER BY` on a column without uniqueness rules |
| **FULLTEXT** | Enables fast natural-language text search inside `TEXT`/`VARCHAR` columns |

---

## 4.2 Creating And Using Indexes

```sql
CREATE INDEX idx_products_name ON products (name);

CREATE UNIQUE INDEX idx_users_email ON users (email);
```

An index can also be added directly inside `CREATE TABLE`, and can span multiple columns (a **composite index**), which speeds up queries that filter on those columns together, in that order.

---

## 4.3 Reading EXPLAIN Output

Prefixing any query with `EXPLAIN` shows how MySQL's query optimizer plans to execute it, without actually running it:

```sql
EXPLAIN SELECT * FROM products WHERE name = 'Keyboard';
```

Key columns to watch in the output:

- **type** — the access method; `ALL` means a full table scan (slow), `ref`/`const` mean an index is being used efficiently.
- **key** — which index (if any) MySQL chose to use.
- **rows** — MySQL's estimate of how many rows it will examine.

---

## 4.4 Common Performance Pitfalls

- **Missing indexes** on columns used in `WHERE`, `JOIN`, or `ORDER BY` clauses, forcing full table scans.
- **Functions wrapped around indexed columns** (e.g. `WHERE YEAR(created_at) = 2026`) — this prevents MySQL from using the index at all.
- **`SELECT \*`** on wide tables when only a few columns are needed, wasting I/O.
- **Over-indexing** — every index speeds up reads but slows down writes, since it must be updated on every insert/update/delete.

[Previous](./[3]-CRUD-And-Querying-In-MySQL.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[5]-Transactions-Users-And-Security.md)