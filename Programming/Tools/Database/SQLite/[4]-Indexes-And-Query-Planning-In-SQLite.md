[Previous](./[3]-CRUD-And-Querying-In-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[5]-Transactions-Concurrency-And-PRAGMAs.md)

*SQLite In Production*

# Lesson 4 - Indexes And Query Planning In SQLite

## 4.1 Index Types In SQLite

SQLite uses **B-tree indexes**, the same general structure introduced in Database Fundamentals ([Lesson 13](../[13]-Indexing-And-Query-Performance.md)). Every table that has a `ROWID` is already effectively indexed on it for free, since rows are physically stored in a B-tree keyed by `ROWID` (or by the `PRIMARY KEY`, for a `WITHOUT ROWID` table).

| Index Type | Purpose |
|---|---|
| **Implicit ROWID / PRIMARY KEY index** | Automatic; not created manually |
| **UNIQUE** | Enforces uniqueness while speeding up lookups |
| **Regular INDEX** | Speeds up `WHERE`/`JOIN`/`ORDER BY` on non-key columns |
| **Partial index** | An index built over only the rows matching a `WHERE` clause |

---

## 4.2 Creating And Using Indexes

```sql
CREATE INDEX idx_products_name ON products (name);

CREATE UNIQUE INDEX idx_users_email ON users (email);

-- Partial index: only indexes rows where the condition is true
CREATE INDEX idx_products_low_stock ON products (name)
WHERE in_stock = 0;
```

A partial index keeps the index smaller and faster to maintain when queries only ever filter for a specific subset of rows, such as unfulfilled orders or out-of-stock products.

---

## 4.3 Reading EXPLAIN QUERY PLAN

Prefixing a query with `EXPLAIN QUERY PLAN` shows, in plain language, how SQLite intends to execute it — without actually running it:

```sql
EXPLAIN QUERY PLAN
SELECT * FROM products WHERE name = 'Keyboard';
```

Key phrases to look for in the output:

- **`SCAN products`** — a full table scan; every row is examined. Fine for tiny tables, a red flag for large ones.
- **`SEARCH products USING INDEX idx_products_name (name=?)`** — the query used an index to jump directly to matching rows.
- **`USING TEMP B-TREE`** — SQLite had to build a temporary sorting structure, often because an `ORDER BY` or `GROUP BY` couldn't use an existing index.

---

## 4.4 Common Performance Pitfalls

- **Missing indexes** on columns used in `WHERE`, `JOIN`, or `ORDER BY`, forcing a full `SCAN`.
- **Stale statistics** — running `ANALYZE;` after major data changes helps SQLite's query planner make better choices about which index to use.
- **Functions wrapped around indexed columns** (e.g. `WHERE lower(name) = 'keyboard'`) prevent SQLite from using a plain index on `name`, unless a matching expression index is created for it.
- **Over-indexing** on a write-heavy table — every index must be updated on every `INSERT`, `UPDATE`, and `DELETE`, so indexes trade write speed for read speed, exactly as in any other relational database.

[Previous](./[3]-CRUD-And-Querying-In-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[5]-Transactions-Concurrency-And-PRAGMAs.md)
