[Previous](./[25]-SQL-Dialects.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md)

# Lesson 26 - Query Optimization & Execution Plans

---

This final lesson ties together Lesson 17 (Indexes) with a deeper look at how databases actually execute queries, and how to diagnose and fix slow ones.

---

## How a query actually runs

When you submit a `SELECT`, the database doesn't run it literally in the order you typed it. Instead, it:

1. **Parses** the SQL into an internal representation
2. **Plans** — the query optimizer considers multiple possible strategies (which indexes to use, what order to join tables in, whether to sort before or after filtering) and estimates the cost of each, using statistics about the data
3. **Executes** the cheapest plan it found

This is why understanding execution plans matters — the optimizer's choices, not your query's literal wording, determine actual performance.

---

## EXPLAIN: seeing the plan

```sql
EXPLAIN SELECT * FROM books WHERE genre = 'Fantasy';
```

This shows the plan *without running the query*. To see actual execution statistics (real time, real row counts) as well:
```sql
-- PostgreSQL
EXPLAIN ANALYZE SELECT * FROM books WHERE genre = 'Fantasy';

-- MySQL
EXPLAIN ANALYZE SELECT * FROM books WHERE genre = 'Fantasy';

-- SQLite
EXPLAIN QUERY PLAN SELECT * FROM books WHERE genre = 'Fantasy';
```

---

## Reading a plan: key things to look for

### Scan type
- **Sequential/full table scan**: reads every row — fine for small tables, expensive for large ones
- **Index scan**: uses an index to jump to matching rows — usually much faster on large, selective queries
- **Index-only scan** (PostgreSQL): the index alone contains everything the query needs, avoiding a separate lookup into the table itself — the fastest option when applicable

### Join strategy
- **Nested loop join**: for each row in the outer table, scan the inner table for matches — efficient when one side is small or well-indexed, expensive otherwise
- **Hash join**: builds an in-memory hash table from one side, then probes it with the other — efficient for large, unsorted datasets
- **Merge join**: requires both inputs sorted on the join key, then merges them in one pass — efficient when data is already sorted (e.g., via an index)

The optimizer picks between these automatically based on estimated table sizes and available indexes — you generally don't choose directly, but understanding *why* it chose one helps you diagnose slow queries.

### Estimated vs actual rows
A large gap between the planner's *estimated* row count and the *actual* row count (visible with `EXPLAIN ANALYZE`) often signals outdated statistics — the fix is usually to refresh them:
```sql
ANALYZE books;          -- PostgreSQL, SQLite
ANALYZE TABLE books;    -- MySQL
```

---

## Common causes of slow queries, and their fixes

| Problem | Fix |
|---|---|
| Missing index on a frequently filtered/joined column | Add an index (Lesson 17) |
| `SELECT *` when only a few columns are needed | Select only needed columns — reduces data transferred and can enable index-only scans |
| Function applied to a column in `WHERE` (e.g. `WHERE UPPER(title) = ...`) | Rewrite to avoid wrapping the column, or add a functional/expression index |
| Leading wildcard in `LIKE '%term'` | Can't use a standard B-tree index efficiently; consider full-text search instead |
| Implicit type conversion (e.g., comparing a text column to a number) | Match types explicitly — mismatches can silently disable index usage |
| Large `OFFSET` for pagination | The database must still scan and discard all skipped rows; use "keyset pagination" (`WHERE id > last_seen_id ORDER BY id LIMIT n`) instead for deep pages |
| Outdated table statistics | Run `ANALYZE` |
| Overly broad `JOIN` before filtering | Filter early (in `WHERE`, or push filters into subqueries/CTEs) so less data flows into expensive joins |

---

## N+1 query problems

A common *application-level* performance bug: running one query to get a list, then looping through it and running one additional query per row (often via an ORM), instead of a single query (or join) that fetches everything at once.
```sql
-- Inefficient: 1 query to get books, then N queries (one per book) for authors
SELECT * FROM books;
-- then, in application code, for each book:
SELECT * FROM authors WHERE author_id = ?;

-- Efficient: 1 query total
SELECT b.*, a.name AS author_name
FROM books b
JOIN authors a ON b.author_id = a.author_id;
```
This isn't a SQL syntax issue — it's a pattern to watch for in how application code *calls* SQL, and it's one of the single most common real-world performance problems in database-backed applications.

---

## Query rewriting techniques

### Avoid unnecessary DISTINCT
`DISTINCT` requires extra sorting/hashing work — only use it when duplicates are genuinely possible and unwanted, not as a reflexive habit.

### Prefer EXISTS over IN for large subqueries
```sql
-- Can be slower on large subquery results
SELECT * FROM authors WHERE author_id IN (SELECT author_id FROM books);

-- Often faster — the database can stop at the first match
SELECT * FROM authors a WHERE EXISTS (SELECT 1 FROM books b WHERE b.author_id = a.author_id);
```
Modern optimizers often rewrite these into equivalent plans automatically, but it's not guaranteed across all databases/versions — worth testing both on your actual data.

### Filter before joining, not after
```sql
-- Less efficient: joins all books first, then filters
SELECT * FROM books b JOIN authors a ON b.author_id = a.author_id WHERE b.price > 50;

-- Often equivalent after optimization, but explicit early filtering
-- (e.g., via a CTE) can help the optimizer, especially on complex queries:
WITH expensive_books AS (SELECT * FROM books WHERE price > 50)
SELECT * FROM expensive_books b JOIN authors a ON b.author_id = a.author_id;
```
Good optimizers often push the filter down automatically regardless of how you write it — but on complex, multi-join queries, explicit filtering can still help, and is always worth checking with `EXPLAIN`.

---

## A practical optimization workflow

1. **Measure first** — don't guess; use `EXPLAIN ANALYZE` to find the actual slow part of a query
2. **Look for full table scans** on large tables where an index-based scan seems like it should apply
3. **Check statistics are current** with `ANALYZE`
4. **Add indexes deliberately**, based on evidence from real query patterns — not preemptively on every column
5. **Re-measure after each change** — optimization is iterative, and changes can have non-obvious interactions
6. **Consider the bigger picture**: sometimes the right fix is denormalization (Lesson 15), a materialized view (Lesson 16), caching in the application layer, or redesigning the query entirely — not just tweaking indexes

---

## You've completed the course! 🎉

You've gone from installing a database to reading query execution plans — covering querying, schema design, integrity, performance, and the practical differences between major database systems along the way.

From here, the best way to keep building SQL skill is to use it: work with a real dataset that interests you, rebuild a small app's data layer, or explore your chosen database's documentation for the deeper corners of features only touched on here (window functions, JSON, full-text search, and query planning all go much further than a single lesson can cover).

---

[Previous](./[25]-SQL-Dialects.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) 
