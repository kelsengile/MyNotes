[Previous](./[3]-Querying-In-PostgreSQL.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[5]-Transactions-And-MVCC.md)

*Advanced PostgreSQL*

# Lesson 4 - Indexes And Query Planning

## 4.1 Index Types (B-tree, GIN, GiST, BRIN)

PostgreSQL supports several index types, each suited to a different kind of data:

| Index Type | Best For |
|---|---|
| **B-tree** (default) | Equality and range queries (`=`, `<`, `>`, `BETWEEN`) — the general-purpose choice |
| **GIN** | Searching inside composite values like arrays, JSONB, and full-text search |
| **GiST** | Geometric data, ranges, and nearest-neighbor searches |
| **BRIN** | Very large tables where data is naturally ordered (e.g. timestamps) — extremely small index size |

---

## 4.2 Creating Indexes

```sql
CREATE INDEX idx_products_name ON products (name);

CREATE INDEX idx_events_payload ON events USING GIN (payload);
```

The `USING` clause selects a non-default index type — here, a GIN index that makes searching inside the `payload` JSONB column fast.

---

## 4.3 EXPLAIN ANALYZE

`EXPLAIN` shows PostgreSQL's planned execution strategy; adding `ANALYZE` actually runs the query and reports real timing:

```sql
EXPLAIN ANALYZE
SELECT * FROM products WHERE name = 'Keyboard';
```

Look for:

- **Seq Scan** — a full table scan (fine for small tables, a red flag for large ones).
- **Index Scan** / **Index Only Scan** — the planner is using an index efficiently.
- **actual time** — real measured execution time, useful for spotting the slowest step in a query.

---

## 4.4 Query Tuning Tips

- Add indexes to columns frequently used in `WHERE`, `JOIN`, and `ORDER BY`.
- Run `ANALYZE tablename;` periodically so the planner has accurate statistics to make good decisions.
- Avoid `SELECT *` when only specific columns are needed.
- Use `EXPLAIN ANALYZE` to test assumptions rather than guessing — the planner's choices can be surprising.

[Previous](./[3]-Querying-In-PostgreSQL.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[5]-Transactions-And-MVCC.md)