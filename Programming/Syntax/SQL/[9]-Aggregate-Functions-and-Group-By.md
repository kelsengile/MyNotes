[Previous](./[8]-Functions-and-CASE-Expressions.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[10]-Set-Operations.md)

# Lesson 9 - Aggregate Functions & GROUP BY

---

Aggregate functions summarize many rows into a single value — "how many," "what's the average," "what's the total." Combined with `GROUP BY`, they let you summarize data *per category* instead of across the whole table.

---

## 9.1 The five core aggregate functions

| Function | Purpose |
|---|---|
| `COUNT(x)` | Number of non-NULL values (or rows, with `COUNT(*)`) |
| `SUM(x)` | Total of a numeric column |
| `AVG(x)` | Average of a numeric column |
| `MIN(x)` | Smallest value |
| `MAX(x)` | Largest value |

Without `GROUP BY`, an aggregate collapses the *entire table* into one row:
```sql
SELECT COUNT(*) FROM books;
SELECT AVG(price) FROM books;
SELECT MIN(published_year), MAX(published_year) FROM books;
```

---

## 9.2 GROUP BY: aggregating per category

`GROUP BY` splits rows into buckets based on one or more columns, then applies aggregate functions *within each bucket*.

```sql
SELECT genre, COUNT(*) AS book_count
FROM books
GROUP BY genre;
```

This returns one row per distinct genre, with a count of how many books fall in it.

```sql
SELECT genre, AVG(price) AS avg_price, MAX(price) AS max_price
FROM books
GROUP BY genre;
```

---

## 9.3 The golden rule of GROUP BY

Every column in `SELECT` that isn't wrapped in an aggregate function **must** appear in `GROUP BY`. This query is invalid in most databases (SQLite is a notable, permissive exception, but relying on that is bad practice):
```sql
-- INVALID in PostgreSQL/MySQL strict mode/SQL Server:
SELECT genre, title, AVG(price)
FROM books
GROUP BY genre;
-- title isn't aggregated and isn't in GROUP BY — which title would it show?
```

---

## 9.4 Grouping by multiple columns

```sql
SELECT genre, published_year, COUNT(*) AS count
FROM books
GROUP BY genre, published_year;
```
Each unique *combination* of genre and year becomes its own group.

---

## 9.5 HAVING: filtering groups

`WHERE` filters rows *before* grouping happens. `HAVING` filters groups *after* aggregation — and it's the only place you can filter based on an aggregate result.

```sql
SELECT genre, COUNT(*) AS book_count
FROM books
GROUP BY genre
HAVING COUNT(*) > 1;
```

This is invalid — you cannot use an aggregate function in `WHERE`:
```sql
-- INVALID:
SELECT genre, COUNT(*) FROM books WHERE COUNT(*) > 1 GROUP BY genre;
```

---

## 9.6 Clause execution order (conceptual)

SQL is *written* in this order:
```
SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY ... LIMIT ...
```
But it's conceptually *evaluated* in a different order:
```
FROM  →  WHERE  →  GROUP BY  →  HAVING  →  SELECT  →  ORDER BY  →  LIMIT
```
This explains several rules that otherwise seem arbitrary:
- `WHERE` can't reference an aggregate, because grouping hasn't happened yet
- `HAVING` can reference an aggregate, because it runs after grouping
- Column aliases defined in `SELECT` generally can't be used in `WHERE` (it runs first) but often *can* be used in `ORDER BY` (it runs last)

---

## 9.7 Combining WHERE and HAVING

```sql
SELECT genre, AVG(price) AS avg_price
FROM books
WHERE published_year > 1970
GROUP BY genre
HAVING AVG(price) > 9;
```
Here, `WHERE` removes individual books published in or before 1970 *before* grouping; `HAVING` then removes entire genre-groups whose average price doesn't clear $9.

---

## 9.8 COUNT(DISTINCT ...)

Counts unique values rather than every row:
```sql
SELECT COUNT(DISTINCT genre) FROM books;
SELECT COUNT(DISTINCT author_id) FROM books;
```

---

## 9.9 A full example

```sql
SELECT
    a.country,
    COUNT(b.book_id) AS total_books,
    ROUND(AVG(b.price), 2) AS avg_price
FROM books b
JOIN authors a ON b.author_id = a.author_id
GROUP BY a.country
HAVING COUNT(b.book_id) >= 1
ORDER BY avg_price DESC;
```
(This uses a `JOIN`, covered fully in Lesson 11 — included here to show how naturally aggregation combines with joins.)

---

[Previous](./[8]-Functions-and-CASE-Expressions.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[10]-Set-Operations.md)
