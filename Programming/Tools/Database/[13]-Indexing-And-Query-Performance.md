[Previous](./[12]-Transactions-And-ACID.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[14]-Concurrency-And-Locking.md)

*Transactions And Performance*

# Lesson 13 - Indexing And Query Performance

## 13.1 What Is an Index

A **database index** is a separate data structure that stores a sorted copy of one or more columns' values, along with a pointer back to the full row they belong to. It works like the index at the back of a textbook: instead of scanning every page to find a topic, you jump straight to the page number listed under that topic.

Without an index, the database has no way to know where a matching row lives, so it has to check every row one by one — this is called a **full table scan**.

```sql
CREATE INDEX idx_users_email ON users (email);
```

This creates an index on the `email` column of the `users` table, so lookups by email no longer require scanning the whole table.

---

## 13.2 How Indexes Speed Up Reads

Most indexes are built using a **B-tree** (balanced tree) structure, which keeps values sorted and lets the database narrow down a search in a small number of steps, rather than checking every row.

```sql
SELECT * FROM users WHERE email = 'ana@example.com';
```

- **Without an index**: the database scans every row in `users`, checking each one's `email` column. This is O(n) — it gets slower as the table grows.
- **With an index on `email`**: the database jumps almost directly to the matching row(s) using the sorted B-tree. This is close to O(log n) — much faster on large tables.

> 💡 **Analogy:** Finding "Zebra" in a phone book by flipping to roughly the last page (because you know it's sorted alphabetically) is an index lookup. Reading every single name from page 1 until you happen to find "Zebra" is a full table scan.

Indexes are especially valuable on columns frequently used in:

- `WHERE` clauses (see [10.1](./[10]-Filtering-Sorting-And-Aggregating.md))
- `JOIN` conditions (see [11.2](./[11]-Joins-And-Subqueries.md))
- `ORDER BY` clauses (see [10.2](./[10]-Filtering-Sorting-And-Aggregating.md))

Primary keys (see [6.1](./[6]-Keys-And-Relationships.md)) and, in most databases, unique constraints are indexed automatically.

---

## 13.3 Trade-offs of Indexing

Indexes aren't free — they come with real costs, so they should be added deliberately rather than on every column:

- **Storage space** — each index is an additional data structure that needs to be stored on disk, alongside the table itself.
- **Slower writes** — every `INSERT`, `UPDATE`, or `DELETE` must also update every index on that table, since the sorted structure needs to stay accurate.
- **Diminishing returns** — indexing a column with very few distinct values (like a `boolean` flag) rarely helps, since the index can't narrow the search down much.

A good rule of thumb: index columns that are read often and selective (they narrow results down a lot), and avoid indexing columns that are written to constantly but rarely searched on.

**🔍 Quick Example:** Indexing a `is_active` boolean column on a 10-million-row table barely helps — since roughly half the rows match either value, the index can't narrow much. Indexing `email`, where every value is nearly unique, narrows a search from 10 million rows down to essentially one instantly.

---

## 13.4 Reading a Query Execution Plan

Most SQL databases let you preview *how* a query will actually be run using `EXPLAIN` (or `EXPLAIN ANALYZE` to also execute it and show real timing). This reveals whether your indexes are actually being used.

```sql
EXPLAIN SELECT * FROM users WHERE email = 'ana@example.com';
```

A simplified output might show one of these strategies:

- **Seq Scan** (sequential/full table scan) — the database is reading every row. This is a sign a helpful index may be missing.
- **Index Scan** — the database used an index to find matching rows directly.

Reading execution plans is one of the most practical skills for diagnosing a slow query: it tells you exactly where the database is spending its time, rather than leaving you to guess.

[Previous](./[12]-Transactions-And-ACID.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[14]-Concurrency-And-Locking.md)
