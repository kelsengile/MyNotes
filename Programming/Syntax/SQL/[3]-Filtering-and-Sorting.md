[Previous](./[2]-SQL-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[4]-Data-Types-and-Table-Design.md)

# Lesson 3 - Filtering & Sorting Data

---

This lesson builds on `WHERE` from Lesson 2, adding more powerful ways to filter, plus how to control the order and quantity of results. It uses the same `books` / `authors` tables from Lesson 2.

---

## Combining conditions: AND, OR, NOT

```sql
SELECT title, price, genre
FROM books
WHERE genre = 'Science Fiction' AND price < 10;
```

`AND` requires both conditions to be true. `OR` requires at least one:
```sql
SELECT title, genre
FROM books
WHERE genre = 'Fantasy' OR genre = 'Science Fiction';
```

`NOT` negates a condition:
```sql
SELECT title FROM books WHERE NOT genre = 'Fantasy';
```

### Operator precedence

`AND` binds tighter than `OR`, which can cause surprises. Always use parentheses when mixing them:
```sql
-- Ambiguous-looking, relies on precedence rules:
SELECT * FROM books WHERE genre = 'Fantasy' OR genre = 'Science Fiction' AND price < 10;

-- Clear and explicit:
SELECT * FROM books WHERE genre = 'Fantasy' OR (genre = 'Science Fiction' AND price < 10);
```

---

## IN: matching a list of values

Instead of chaining `OR` conditions on the same column, use `IN`:
```sql
SELECT title, genre
FROM books
WHERE genre IN ('Fantasy', 'Science Fiction', 'Magical Realism');
```
`NOT IN` excludes a list:
```sql
SELECT title FROM books WHERE genre NOT IN ('Fantasy');
```

---

## BETWEEN: matching a range

```sql
SELECT title, published_year
FROM books
WHERE published_year BETWEEN 1970 AND 2000;
```
`BETWEEN` is inclusive on both ends — it includes 1970 and 2000.

---

## LIKE: pattern matching text

`LIKE` uses two wildcards:
- `%` matches any sequence of characters (including none)
- `_` matches exactly one character

```sql
SELECT title FROM books WHERE title LIKE 'The%';        -- starts with "The"
SELECT title FROM books WHERE title LIKE '%Wood';        -- ends with "Wood"
SELECT title FROM books WHERE title LIKE '%of%';         -- contains "of" anywhere
```
`LIKE` is case-insensitive in SQLite and MySQL by default, but case-sensitive in PostgreSQL (use `ILIKE` there for case-insensitive matching).

---

## IS NULL: checking for missing values

`NULL` (covered fully in Lesson 7) represents missing data and needs special handling — you can't use `= NULL`.
```sql
SELECT title FROM books WHERE author_id IS NULL;
SELECT title FROM books WHERE author_id IS NOT NULL;
```

---

## ORDER BY: sorting results

```sql
SELECT title, price FROM books ORDER BY price;          -- ascending by default
SELECT title, price FROM books ORDER BY price DESC;      -- descending
```

Sort by multiple columns — later columns break ties in earlier ones:
```sql
SELECT title, genre, price
FROM books
ORDER BY genre ASC, price DESC;
```

You can also order by column position (less readable, but valid):
```sql
SELECT title, price FROM books ORDER BY 2 DESC;
```

---

## LIMIT and OFFSET: controlling how many rows come back

```sql
SELECT title, price FROM books ORDER BY price DESC LIMIT 3;
```
`OFFSET` skips a number of rows first — useful for pagination:
```sql
SELECT title, price FROM books ORDER BY price DESC LIMIT 3 OFFSET 3;
```
This pattern returns "page 2" of 3-row pages.

**Dialect note:** SQLite, PostgreSQL, and MySQL all support `LIMIT`/`OFFSET`. SQL Server traditionally uses `TOP` or `OFFSET ... FETCH NEXT ... ROWS ONLY` instead (see Lesson 25).

---

## DISTINCT: removing duplicate rows

```sql
SELECT DISTINCT genre FROM books;
```
This returns each unique genre once, even if many books share it. `DISTINCT` applies to the whole row being selected, not just one column:
```sql
SELECT DISTINCT genre, published_year FROM books;
```
This only collapses rows where *both* genre and year match exactly.

---

## Putting it together

Full clause order so far:
```sql
SELECT columns
FROM table
WHERE conditions
ORDER BY columns
LIMIT n OFFSET m;
```

Example:
```sql
SELECT title, genre, price
FROM books
WHERE price BETWEEN 8 AND 12
ORDER BY price ASC
LIMIT 2;
```

---

[Previous](./[2]-SQL-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[4]-Data-Types-and-Table-Design.md)
