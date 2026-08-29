[Previous](./[15]-Normalization-and-Schema-Design.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[17]-Indexes-and-Performance.md)

# Lesson 16 - Views

---

A **view** is a saved query that behaves like a virtual table — you can `SELECT` from it just like a real table, but it doesn't store data itself; it re-runs its underlying query every time you use it.

---

## Creating a view

```sql
CREATE VIEW expensive_books AS
SELECT title, price, genre
FROM books
WHERE price > 10;
```

Now you can query it exactly like a table:
```sql
SELECT * FROM expensive_books;
SELECT title FROM expensive_books WHERE genre = 'Magical Realism';
```
Behind the scenes, the database expands this into the original query with your additional filter combined — there's no separately-stored data.

---

## Why use views?

### 1. Simplifying complex queries
If you frequently run a query with several joins and aggregations, wrapping it in a view means everyone can just say `SELECT * FROM sales_summary` instead of retyping the underlying logic every time:
```sql
CREATE VIEW book_details AS
SELECT b.title, b.price, b.genre, a.name AS author_name, a.country
FROM books b
JOIN authors a ON b.author_id = a.author_id;
```

### 2. Restricting access to sensitive columns
A view can expose only some columns of a table, letting you grant access to the view without exposing the full underlying table (ties into Lesson 23, permissions):
```sql
CREATE VIEW public_employee_directory AS
SELECT name, department, work_email
FROM employees;
-- (deliberately omits salary, home_address, ssn, etc.)
```

### 3. Providing a stable interface
If the underlying tables' structure changes, a view can be updated to match while the queries built on top of it keep working unchanged — insulating downstream code from schema churn.

### 4. Reusability and consistency
Encoding business logic (like "what counts as an active customer") once, in a view, avoids every analyst or application reinventing — and possibly getting subtly wrong — that same logic repeatedly.

---

## Updating through a view

Some views are **updatable** — you can `INSERT`, `UPDATE`, or `DELETE` through them, and the change is applied to the underlying table:
```sql
UPDATE expensive_books SET price = 14.99 WHERE title = 'Kafka on the Shore';
```

A view is generally updatable only if it's "simple" — no aggregates (`GROUP BY`, `SUM`, etc.), no `DISTINCT`, no joins across multiple tables, and it maps cleanly to one underlying table's rows and columns. Complex views (most joins, aggregates) are typically read-only.

---

## Dropping and replacing a view

```sql
DROP VIEW expensive_books;

-- PostgreSQL / SQLite: redefine directly
CREATE OR REPLACE VIEW expensive_books AS
SELECT title, price FROM books WHERE price > 12;
```
**Dialect note:** `CREATE OR REPLACE VIEW` works in PostgreSQL and MySQL. Older SQLite doesn't support `OR REPLACE` for views — you must `DROP VIEW` first, then `CREATE VIEW` again.

---

## Materialized views

A **materialized view** is a variant that *does* physically store its query's results, refreshed on demand or on a schedule, rather than recomputing on every access. This trades freshness for speed — ideal for expensive aggregate queries that don't need to reflect the absolute latest data.

```sql
-- PostgreSQL
CREATE MATERIALIZED VIEW genre_stats AS
SELECT genre, COUNT(*) AS book_count, AVG(price) AS avg_price
FROM books
GROUP BY genre;

-- Refresh it later, once the underlying data has changed:
REFRESH MATERIALIZED VIEW genre_stats;
```

**Dialect note:** Materialized views are natively supported in PostgreSQL and Oracle. MySQL has no built-in materialized view feature (people simulate them with a real table plus a scheduled event). SQLite has no materialized views at all — you'd manage a regular table manually instead. SQL Server calls the equivalent concept an "indexed view."

---

## Views vs CTEs (preview of Lesson 19)

Both let you name and reuse a piece of query logic — the key difference is persistence. A view is saved permanently in the database schema and can be used across many different queries and sessions. A CTE (`WITH ... AS (...)`) exists only for the duration of a single query.

---

[Previous](./[15]-Normalization-and-Schema-Design.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[17]-Indexes-and-Performance.md)
