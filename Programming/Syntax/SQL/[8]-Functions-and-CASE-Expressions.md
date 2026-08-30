[Previous](./[7]-Handling-NULLs.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[9]-Aggregate-Functions-and-Group-By.md)

# Lesson 8 - String, Date & Math Functions and CASE Expressions

---

Built-in functions let you transform and compute values inside a query, instead of pulling raw data and processing it elsewhere. Function names and behavior vary more between database systems than almost anything else in SQL — this lesson shows common patterns, with dialect notes.

---

## 8.1 String functions

| Task | SQLite / PostgreSQL | MySQL | SQL Server |
|---|---|---|---|
| Uppercase | `UPPER(x)` | same | same |
| Lowercase | `LOWER(x)` | same | same |
| Length | `LENGTH(x)` | `LENGTH(x)` | `LEN(x)` |
| Concatenate | `x \|\| y` | `CONCAT(x, y)` | `x + y` or `CONCAT(x, y)` |
| Substring | `SUBSTR(x, start, len)` | same | `SUBSTRING(x, start, len)` |
| Trim whitespace | `TRIM(x)` | same | same |
| Replace text | `REPLACE(x, old, new)` | same | same |

Examples:
```sql
SELECT UPPER(title) FROM books;
SELECT title || ' (' || published_year || ')' AS display_name FROM books;   -- SQLite/Postgres
SELECT CONCAT(title, ' (', published_year, ')') AS display_name FROM books; -- MySQL, works in Postgres too
SELECT TRIM('  hello  ');   -- 'hello'
SELECT SUBSTR('Kafka on the Shore', 1, 5);   -- 'Kafka'
```

---

## 8.2 Math functions

| Function | Purpose |
|---|---|
| `ROUND(x, n)` | Round to n decimal places |
| `ABS(x)` | Absolute value |
| `CEIL(x)` / `CEILING(x)` | Round up |
| `FLOOR(x)` | Round down |
| `POWER(x, y)` | x raised to power y |
| `SQRT(x)` | Square root |
| `MOD(x, y)` or `x % y` | Remainder |

```sql
SELECT title, ROUND(price, 1) AS rounded_price FROM books;
SELECT ABS(-5);        -- 5
SELECT CEIL(4.1);      -- 5
```

---

## 8.3 Date and time functions

Date functions vary the most across dialects. Examples for getting "today":

```sql
SELECT DATE('now');              -- SQLite
SELECT CURRENT_DATE;             -- PostgreSQL, MySQL, SQL Server (standard)
```

Extracting parts of a date:
```sql
-- PostgreSQL
SELECT EXTRACT(YEAR FROM order_date) FROM orders;

-- MySQL
SELECT YEAR(order_date) FROM orders;

-- SQLite
SELECT STRFTIME('%Y', order_date) FROM orders;

-- SQL Server
SELECT YEAR(order_date) FROM orders;
```

Date arithmetic:
```sql
-- PostgreSQL
SELECT order_date + INTERVAL '7 days' FROM orders;

-- MySQL
SELECT DATE_ADD(order_date, INTERVAL 7 DAY) FROM orders;

-- SQLite
SELECT DATE(order_date, '+7 days') FROM orders;

-- SQL Server
SELECT DATEADD(day, 7, order_date) FROM orders;
```

Because of this variation, always check your specific database's documentation when working with dates — it's the area of SQL with the least standardization.

---

## 8.4 Type conversion: CAST

`CAST` converts a value from one type to another, and is standard across all major databases:
```sql
SELECT CAST(published_year AS TEXT) FROM books;
SELECT CAST('42' AS INTEGER);
SELECT CAST(price AS INTEGER) FROM books;   -- truncates decimals
```
Shorthand in PostgreSQL and SQLite: `price::INTEGER`.

---

## 8.5 CASE expressions: conditional logic inside a query

`CASE` is SQL's if/else — it lets you compute a value based on conditions, inline in a `SELECT`, `WHERE`, or `ORDER BY`.

### Simple CASE (matches exact values)
```sql
SELECT title,
    CASE genre
        WHEN 'Science Fiction' THEN 'Sci-Fi'
        WHEN 'Fantasy' THEN 'Fantasy/Sci-Fi'
        ELSE genre
    END AS display_genre
FROM books;
```

### Searched CASE (matches conditions — more flexible, more common)
```sql
SELECT title, price,
    CASE
        WHEN price < 9 THEN 'Budget'
        WHEN price BETWEEN 9 AND 12 THEN 'Standard'
        ELSE 'Premium'
    END AS price_tier
FROM books;
```

Conditions are checked top to bottom, and the first match wins. `ELSE` is optional — if omitted and nothing matches, the result is `NULL`.

### CASE inside WHERE or ORDER BY
```sql
SELECT title FROM books
ORDER BY CASE WHEN genre = 'Fantasy' THEN 0 ELSE 1 END, title;
```
This pushes all Fantasy books to the top, then sorts alphabetically within each group.

### CASE for conditional aggregation (preview of Lesson 9)
```sql
SELECT
    COUNT(CASE WHEN price < 10 THEN 1 END) AS cheap_books,
    COUNT(CASE WHEN price >= 10 THEN 1 END) AS expensive_books
FROM books;
```

---

[Previous](./[7]-Handling-NULLs.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[9]-Aggregate-Functions-and-Group-By.md)
