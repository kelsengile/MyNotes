[Previous](./[19]-CTEs-and-Recursive-Queries.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[21]-Stored-Procedures-and-Functions.md)

# Lesson 20 - Window Functions

---

## The problem window functions solve

`GROUP BY` (Lesson 9) collapses many rows into one row per group — you lose the individual rows. **Window functions** let you calculate aggregates and rankings *while keeping every individual row visible*, by computing across a "window" of related rows for each row.

---

## Basic syntax: OVER()

```sql
SELECT title, genre, price,
    AVG(price) OVER () AS overall_avg_price
FROM books;
```
This attaches the overall average price to *every single row*, without collapsing anything — very different from `GROUP BY`, which would leave you with just one row.

---

## PARTITION BY: windows within groups

`PARTITION BY` splits rows into groups (like `GROUP BY` does), but each row of the original table is still kept in the output:
```sql
SELECT title, genre, price,
    AVG(price) OVER (PARTITION BY genre) AS avg_price_in_genre
FROM books;
```
Every row shows its own title/price *and* the average price for its genre, side by side — impossible to do directly with plain `GROUP BY`.

---

## Ranking functions

### ROW_NUMBER()
Assigns a unique, sequential number to each row within its partition:
```sql
SELECT title, genre, price,
    ROW_NUMBER() OVER (PARTITION BY genre ORDER BY price DESC) AS rank_in_genre
FROM books;
```
This numbers books 1, 2, 3... within each genre, ordered by price descending — the most expensive book in each genre gets rank 1.

### RANK() and DENSE_RANK()
Both assign ranks based on `ORDER BY`, but handle ties differently:
```sql
SELECT title, price,
    RANK() OVER (ORDER BY price DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY price DESC) AS dense_rank
FROM books;
```
- `RANK()`: ties get the same rank, but the next rank *skips* — e.g., `1, 2, 2, 4`
- `DENSE_RANK()`: ties get the same rank, and the next rank does *not* skip — e.g., `1, 2, 2, 3`
- `ROW_NUMBER()`: every row gets a unique number regardless of ties, arbitrarily breaking them — e.g., `1, 2, 3, 4`

### NTILE(n)
Divides rows into `n` roughly equal buckets — useful for quartiles, deciles, etc.:
```sql
SELECT title, price, NTILE(4) OVER (ORDER BY price) AS price_quartile
FROM books;
```

---

## A practical use of ranking: "top N per group"

```sql
WITH ranked AS (
    SELECT title, genre, price,
        ROW_NUMBER() OVER (PARTITION BY genre ORDER BY price DESC) AS rn
    FROM books
)
SELECT title, genre, price
FROM ranked
WHERE rn = 1;
```
This finds the single most expensive book in *each* genre — a very common reporting need that's awkward to express without window functions.

Note: you **cannot** use a window function's result directly in the same query's `WHERE` clause — the CTE wrapper above is the standard workaround, since `WHERE` is conceptually evaluated before window functions run (similar to why `HAVING` exists for aggregates — see Lesson 9).

---

## Offset functions: LAG() and LEAD()

These let a row see values from a *neighboring* row within its partition — perfect for period-over-period comparisons:
```sql
SELECT title, published_year, price,
    LAG(price) OVER (ORDER BY published_year) AS prev_book_price,
    LEAD(price) OVER (ORDER BY published_year) AS next_book_price
FROM books;
```
`LAG` looks backward, `LEAD` looks forward. Both accept an optional second argument for how many rows to look back/forward (default 1), and a third for a default value when there's no such row:
```sql
LAG(price, 1, 0) OVER (ORDER BY published_year)
```

---

## Running totals with window frames

By default, aggregate window functions with `ORDER BY` compute over "all rows from the start of the partition up to the current row" — giving you a running total:
```sql
SELECT title, published_year, price,
    SUM(price) OVER (ORDER BY published_year) AS running_total
FROM books;
```

You can control the window frame explicitly:
```sql
SELECT title, published_year, price,
    SUM(price) OVER (
        ORDER BY published_year
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS rolling_3row_sum
FROM books;
```
This computes a moving sum over the current row plus the two preceding it — a rolling window, common in time-series analysis.

---

## FIRST_VALUE() and LAST_VALUE()

```sql
SELECT title, genre, price,
    FIRST_VALUE(title) OVER (PARTITION BY genre ORDER BY price DESC) AS priciest_in_genre
FROM books;
```

---

## Window functions vs GROUP BY: when to use which

- Use `GROUP BY` when you want a summarized result — one row per group, individual rows discarded.
- Use a window function when you want to keep every individual row but *enrich* it with group-level context, running calculations, or rankings.

---

[Previous](./[19]-CTEs-and-Recursive-Queries.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[21]-Stored-Procedures-and-Functions.md)
