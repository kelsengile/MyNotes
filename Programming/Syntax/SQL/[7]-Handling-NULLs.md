[Previous](./[6]-Insert-Update-Delete.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[8]-Functions-and-CASE-Expressions.md)

# Lesson 7 - Handling NULLs

---

## What NULL means

`NULL` represents the *absence* of a value — "unknown" or "not applicable," not zero, not an empty string, and not false. A book with no known `published_year` should store `NULL`, not `0`.

```sql
INSERT INTO books (book_id, title, author_id, price, published_year, genre)
VALUES (8, 'Untitled Manuscript', NULL, 15.00, NULL, NULL);
```

---

## Why NULL = NULL doesn't work

This is the single most common NULL-related bug. In SQL, `NULL` isn't equal to anything — not even to another `NULL` — because "unknown" compared to "unknown" is itself unknown.

```sql
SELECT * FROM books WHERE author_id = NULL;      -- returns NOTHING, always
SELECT * FROM books WHERE author_id != NULL;     -- also returns NOTHING
```

Use `IS NULL` / `IS NOT NULL` instead:
```sql
SELECT * FROM books WHERE author_id IS NULL;
SELECT * FROM books WHERE author_id IS NOT NULL;
```

---

## NULL in calculations

Any arithmetic involving `NULL` produces `NULL`:
```sql
SELECT price + NULL AS result;  -- result is NULL, not price
```

---

## NULL in AND / OR logic

SQL uses three-valued logic: `TRUE`, `FALSE`, and `UNKNOWN` (which behaves like NULL). A row is only returned by `WHERE` if the condition evaluates to `TRUE` — `UNKNOWN` rows are excluded, same as `FALSE` rows.

| A | B | A AND B | A OR B |
|---|---|---|---|
| TRUE | NULL | NULL | TRUE |
| FALSE | NULL | FALSE | NULL |
| NULL | NULL | NULL | NULL |

This matters when combining a NULL-producing condition with `NOT`:
```sql
-- If author_id is NULL, this row is excluded from BOTH queries below:
SELECT * FROM books WHERE author_id = 1;
SELECT * FROM books WHERE NOT (author_id = 1);   -- NOT UNKNOWN is still UNKNOWN!
```

---

## COALESCE: substituting a default value

`COALESCE` returns the first non-NULL value from a list of arguments — the standard way to provide a fallback:
```sql
SELECT title, COALESCE(published_year, 0) AS year_or_zero
FROM books;

SELECT title, COALESCE(genre, 'Unclassified') AS genre
FROM books;
```
`COALESCE` can take more than two arguments and returns the first one that isn't `NULL`:
```sql
SELECT COALESCE(nickname, first_name, 'Anonymous') FROM users;
```

---

## NULLIF: turning a value into NULL conditionally

`NULLIF(a, b)` returns `NULL` if `a` equals `b`, otherwise returns `a`. Useful for avoiding divide-by-zero errors:
```sql
SELECT total_sales / NULLIF(total_orders, 0) AS avg_order_value
FROM stats;
```
If `total_orders` is 0, this returns `NULL` instead of throwing a division error.

---

## NULLs and aggregate functions

Aggregate functions (fully covered in Lesson 9) generally *ignore* NULLs rather than treating them as zero:
```sql
SELECT AVG(published_year) FROM books;   -- ignores rows where published_year IS NULL
SELECT COUNT(published_year) FROM books; -- counts only non-NULL values
SELECT COUNT(*) FROM books;              -- counts ALL rows, NULL or not
```
This distinction between `COUNT(column)` and `COUNT(*)` trips up a lot of beginners.

---

## NULLs and sorting

By default, most databases sort `NULL` values as either first or last, but the exact behavior differs:
- PostgreSQL: `NULL` sorts last in `ASC` order by default
- MySQL and SQLite: `NULL` sorts first in `ASC` order by default

To control this explicitly (PostgreSQL, and SQL:2003 standard):
```sql
SELECT title, published_year
FROM books
ORDER BY published_year ASC NULLS LAST;
```
**Dialect note:** `NULLS FIRST`/`NULLS LAST` isn't supported in MySQL or SQL Server; you'd typically use a `CASE` expression as a workaround there.

---

## NULLs and UNIQUE constraints

A `UNIQUE` constraint (Lesson 14) generally allows multiple `NULL` values in the same column, since `NULL` is never considered equal to another `NULL` — even under a uniqueness check. This behavior is consistent across PostgreSQL, SQLite, and MySQL.

---

[Previous](./[6]-Insert-Update-Delete.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[8]-Functions-and-CASE-Expressions.md)
