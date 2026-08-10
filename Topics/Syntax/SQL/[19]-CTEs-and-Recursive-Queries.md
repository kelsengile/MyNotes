# Lesson 19: Common Table Expressions (CTEs) & Recursive Queries

---

## What a CTE is

A **Common Table Expression** (CTE) is a named, temporary result set defined with `WITH`, that you can reference like a table for the rest of that single query. It exists only for the duration of the query — unlike a view (Lesson 16), nothing is saved to the database.

```sql
WITH cheap_books AS (
    SELECT title, price, genre FROM books WHERE price < 10
)
SELECT genre, COUNT(*) AS count
FROM cheap_books
GROUP BY genre;
```

## Why use a CTE instead of a subquery?

Both often produce identical results and identical performance (modern query optimizers frequently treat them the same way internally) — the real difference is **readability**. Compare:

```sql
-- Nested subquery: read from the inside out
SELECT genre, AVG(price)
FROM (
    SELECT genre, price FROM books WHERE published_year > 1970
) recent_books
GROUP BY genre;

-- CTE: read top to bottom, name explains intent
WITH recent_books AS (
    SELECT genre, price FROM books WHERE published_year > 1970
)
SELECT genre, AVG(price)
FROM recent_books
GROUP BY genre;
```
As logic gets more nested, CTEs stay readable while stacked subqueries quickly become hard to follow.

## Multiple CTEs in one query

Separate multiple CTEs with commas — later CTEs can reference earlier ones:
```sql
WITH scifi_books AS (
    SELECT * FROM books WHERE genre = 'Science Fiction'
),
scifi_avg AS (
    SELECT AVG(price) AS avg_price FROM scifi_books
)
SELECT title, price
FROM scifi_books, scifi_avg
WHERE scifi_books.price > scifi_avg.avg_price;
```

## Referencing a CTE more than once

A CTE can be referenced multiple times within the same query, without the underlying query being re-run and rewritten each time:
```sql
WITH genre_totals AS (
    SELECT genre, COUNT(*) AS total FROM books GROUP BY genre
)
SELECT g1.genre, g1.total
FROM genre_totals g1
WHERE g1.total = (SELECT MAX(total) FROM genre_totals);
```

## Recursive CTEs

A **recursive CTE** references itself, letting you walk through hierarchical or graph-like data — organizational charts, category trees, bill-of-materials structures, or generating a sequence of numbers.

### Structure of a recursive CTE
```sql
WITH RECURSIVE cte_name AS (
    -- Base case: the starting row(s)
    SELECT ...

    UNION ALL

    -- Recursive case: references cte_name itself
    SELECT ...
    FROM cte_name
    JOIN ... -- builds the next "layer"
)
SELECT * FROM cte_name;
```

### Example: an employee hierarchy

```sql
CREATE TABLE employees (
    employee_id INTEGER PRIMARY KEY,
    name TEXT,
    manager_id INTEGER
);

INSERT INTO employees VALUES
    (1, 'Dara (CEO)', NULL),
    (2, 'Femi (VP)', 1),
    (3, 'Grace (Manager)', 2),
    (4, 'Hakeem (Engineer)', 3);

WITH RECURSIVE org_chart AS (
    -- Base case: start at the top (no manager)
    SELECT employee_id, name, manager_id, 0 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive case: find employees whose manager is already in org_chart
    SELECT e.employee_id, e.name, e.manager_id, oc.level + 1
    FROM employees e
    JOIN org_chart oc ON e.manager_id = oc.employee_id
)
SELECT * FROM org_chart ORDER BY level;
```

This starts with the CEO (level 0), then finds everyone reporting to the CEO (level 1), then everyone reporting to *them* (level 2), and so on, until no more matches are found.

### Example: generating a sequence of numbers

```sql
WITH RECURSIVE counter AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM counter WHERE n < 10
)
SELECT n FROM counter;
```
This produces the numbers 1 through 10 — useful for generating date ranges, filling gaps in data, or building calendars.

## Avoiding infinite recursion

A recursive CTE without a stopping condition will run forever (or until it hits a database-imposed row/depth limit). Always ensure the recursive case eventually stops matching new rows — in the examples above, that happens naturally once no more employees or numbers satisfy the join/filter condition.

**Dialect note:** `WITH RECURSIVE` is the standard syntax (PostgreSQL, SQLite, MySQL 8.0+). SQL Server uses `WITH cte_name AS (...)` *without* the word `RECURSIVE` — the recursion is implicit if the CTE references itself.

## CTEs in INSERT/UPDATE/DELETE

CTEs aren't limited to `SELECT` — they can feed other statements too:
```sql
WITH overpriced AS (
    SELECT book_id FROM books WHERE price > 50
)
DELETE FROM books WHERE book_id IN (SELECT book_id FROM overpriced);
```

---

## Exercises

1. Rewrite this subquery as a CTE:
   ```sql
   SELECT * FROM (SELECT genre, AVG(price) AS avg_price FROM books GROUP BY genre) sub WHERE avg_price > 9;
   ```
2. Write a CTE that finds the most expensive book per genre (hint: combine with a second CTE for genre max prices).
3. Write a recursive CTE that generates the numbers 1 through 5.
4. Using the `employees` table above, write a recursive CTE that finds everyone who (directly or indirectly) reports to Femi (employee_id 2).

### Answers

```sql
-- 1
WITH genre_avg AS (
    SELECT genre, AVG(price) AS avg_price FROM books GROUP BY genre
)
SELECT * FROM genre_avg WHERE avg_price > 9;

-- 2
WITH genre_max AS (
    SELECT genre, MAX(price) AS max_price FROM books GROUP BY genre
)
SELECT b.title, b.genre, b.price
FROM books b
JOIN genre_max gm ON b.genre = gm.genre AND b.price = gm.max_price;

-- 3
WITH RECURSIVE counter AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM counter WHERE n < 5
)
SELECT n FROM counter;

-- 4
WITH RECURSIVE reports AS (
    SELECT employee_id, name, manager_id FROM employees WHERE manager_id = 2
    UNION ALL
    SELECT e.employee_id, e.name, e.manager_id
    FROM employees e
    JOIN reports r ON e.manager_id = r.employee_id
)
SELECT * FROM reports;
```

