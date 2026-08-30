[Previous](./[16]-Views.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[18]-Transactions-and-ACID.md)


# Lesson 17 - Indexes & Query Performance


---

## 17.1 The problem indexes solve

Without any help, finding rows matching a `WHERE` condition means the database checks every single row in the table, one by one — a **full table scan**. On a table with a few hundred rows, that's instant. On a table with 50 million rows, it can take seconds or minutes.

An **index** is a separate, ordered data structure that lets the database jump straight to matching rows, similar to how a book's index lets you find a topic without reading every page.

---

## 17.2 Creating an index

```sql
CREATE INDEX idx_books_genre ON books (genre);
```
Now queries filtering on `genre` can use the index instead of scanning the whole table:
```sql
SELECT * FROM books WHERE genre = 'Fantasy';
```

---

## 17.3 How indexes work (conceptually)

Most indexes use a **B-tree** structure: a balanced, sorted tree that lets the database narrow down to matching rows in roughly `O(log n)` steps instead of checking all `n` rows. Think of it like binary search on a sorted list, but able to handle inserts and deletes efficiently as the table changes.

---

## 17.4 Primary keys and unique constraints are automatically indexed

You rarely need to manually index a primary key or a `UNIQUE` column — most databases create an index for these automatically, since uniqueness checks require fast lookups anyway.

---

## 17.5 Composite (multi-column) indexes

```sql
CREATE INDEX idx_books_genre_year ON books (genre, published_year);
```
This helps queries filtering on `genre` alone, or on `genre` *and* `published_year` together — but generally **not** queries filtering on `published_year` alone. Column order in a composite index matters: put the column used most often (or most selectively) first.

---

## 17.6 Checking whether a query uses an index: EXPLAIN

```sql
-- PostgreSQL / SQLite / MySQL
EXPLAIN SELECT * FROM books WHERE genre = 'Fantasy';

-- PostgreSQL, with actual timing:
EXPLAIN ANALYZE SELECT * FROM books WHERE genre = 'Fantasy';
```
This shows the database's **query plan** — whether it used an index scan (fast) or a sequential/full table scan (slow on large tables). Lesson 26 covers reading these plans in detail.

---

## 17.7 When an index helps

- Columns frequently used in `WHERE` clauses
- Columns used in `JOIN ... ON` conditions (foreign keys especially — these are *not* always auto-indexed, unlike primary keys, and are a common performance gap)
- Columns used in `ORDER BY`, since a pre-sorted index can avoid a separate sort step
- High-cardinality columns (many distinct values) tend to benefit more than low-cardinality ones

---

## 17.8 When an index doesn't help (or actively hurts)

- **Small tables** — a full scan of 100 rows is already near-instant; the index adds overhead for no benefit.
- **Low-cardinality columns** — indexing a `boolean` column with only `TRUE`/`FALSE` rarely helps, since roughly half the table matches either value anyway.
- **Columns rarely used for filtering or sorting** — an unused index is pure cost.
- **Write-heavy tables** — every `INSERT`, `UPDATE`, or `DELETE` must also update every index on that table, so more indexes mean slower writes. Indexing is a genuine trade-off between read speed and write speed.
- **Functions applied to the column in WHERE** — `WHERE UPPER(title) = 'KAFKA'` generally can't use a plain index on `title`, since the stored values don't match what's being searched for (some databases support "functional" or "expression" indexes specifically for this case).

---

## 17.9 Types of indexes (brief overview)

| Type | Best for |
|---|---|
| B-tree (default) | General-purpose; equality and range queries (`=`, `<`, `BETWEEN`) |
| Hash | Pure equality lookups (`=`); no range support |
| Full-text | Searching within large text fields (`LIKE '%word%'`-style, but much faster) |
| Spatial (GiST/R-tree) | Geographic/geometric data |
| Partial | Indexes only a subset of rows matching a condition |

```sql
-- PostgreSQL: partial index — only indexes rows meeting a condition
CREATE INDEX idx_expensive_books ON books (price) WHERE price > 20;
```

---

## 17.10 Dropping an index

```sql
DROP INDEX idx_books_genre;             -- SQLite, PostgreSQL
DROP INDEX idx_books_genre ON books;    -- MySQL requires the table name
```

---

## 17.11 A practical mental model

Add an index when you can answer "yes" to: *"Will this column be searched, joined, or sorted on often enough that the write-time cost of maintaining the index is worth the read-time savings?"* Start without extra indexes, measure real query performance with `EXPLAIN`, and add indexes where the evidence points — rather than guessing upfront.

---

[Previous](./[16]-Views.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[18]-Transactions-and-ACID.md)
