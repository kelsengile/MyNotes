# Lesson 12: Subqueries & Nested Queries

---

A subquery is a `SELECT` statement nested inside another SQL statement. It lets you use the result of one query as input to another, without a separate step.

## Scalar subqueries: a single value

A subquery that returns exactly one row and one column can be used anywhere a single value is expected:
```sql
SELECT title, price
FROM books
WHERE price > (SELECT AVG(price) FROM books);
```
This finds books priced above the *average* price of all books — something impossible to express with a plain `WHERE` comparing to a literal, since the average has to be computed first.

Scalar subqueries also work inside `SELECT`:
```sql
SELECT title, price, (SELECT AVG(price) FROM books) AS overall_avg
FROM books;
```

## Subqueries with IN

When a subquery returns multiple rows (but one column), use `IN` to check membership:
```sql
SELECT name
FROM authors
WHERE author_id IN (SELECT author_id FROM books WHERE genre = 'Science Fiction');
```
This finds authors who have written at least one science fiction book.

`NOT IN` finds the opposite — but be careful: if the subquery can return `NULL`, `NOT IN` can unexpectedly return zero rows (Lesson 7's NULL logic strikes again). `NOT EXISTS` (below) is safer.

## Subqueries with comparison operators + ANY/ALL

```sql
SELECT title, price
FROM books
WHERE price > ANY (SELECT price FROM books WHERE genre = 'Fantasy');

SELECT title, price
FROM books
WHERE price > ALL (SELECT price FROM books WHERE genre = 'Fantasy');
```
`> ANY` means "greater than *at least one*" (Fantasy row); `> ALL` means "greater than *every*" Fantasy row.

## EXISTS: checking for any matching row

`EXISTS` returns `TRUE` if the subquery returns at least one row — it doesn't care about the actual values, just whether any rows come back. This is often the fastest way to check for existence, since the database can stop as soon as it finds one match.

```sql
SELECT name
FROM authors a
WHERE EXISTS (SELECT 1 FROM books b WHERE b.author_id = a.author_id AND b.genre = 'Fantasy');
```
This is a **correlated subquery** — notice it references `a.author_id` from the *outer* query. It re-runs (conceptually) once per outer row, unlike the earlier, uncorrelated `IN` example, which runs once total.

`NOT EXISTS` is the standard, NULL-safe way to find "rows with no match":
```sql
SELECT name
FROM authors a
WHERE NOT EXISTS (SELECT 1 FROM books b WHERE b.author_id = a.author_id);
```
This finds authors with zero books in the table — and unlike `NOT IN`, it isn't tripped up if `author_id` contains `NULL`s in the subquery.

## Correlated vs uncorrelated subqueries

- **Uncorrelated**: the subquery is independent — it could run on its own. `WHERE price > (SELECT AVG(price) FROM books)` doesn't need anything from the outer query.
- **Correlated**: the subquery references a column from the outer query, so it logically depends on each outer row. These are typically slower on large tables, since (conceptually) they must be evaluated per row, though modern query optimizers often rewrite them into joins internally.

## Subqueries in FROM (derived tables)

A subquery can stand in for a table entirely — this is often called a "derived table" or "inline view":
```sql
SELECT genre, avg_price
FROM (
    SELECT genre, AVG(price) AS avg_price
    FROM books
    GROUP BY genre
) AS genre_averages
WHERE avg_price > 9;
```
The subquery must be given an alias (`AS genre_averages`) in most databases, including MySQL and PostgreSQL.

## Subqueries vs JOINs vs CTEs

- A subquery in `WHERE ... IN (...)` or `EXISTS` is great for filtering based on a *related fact*, without needing the related table's columns in your output.
- A `JOIN` (Lesson 11) is better when you need columns from *both* tables in your result.
- A CTE (Lesson 19) is often better than a deeply nested subquery for *readability*, especially when the same subquery logic is needed more than once.

These often produce equivalent results — choosing between them is largely about readability and, for large datasets, performance (Lesson 26).

## Subqueries in UPDATE and DELETE

```sql
UPDATE books
SET price = price * 0.9
WHERE author_id IN (SELECT author_id FROM authors WHERE country = 'USA');

DELETE FROM orders
WHERE customer_id NOT IN (SELECT customer_id FROM customers);
```

---

## Exercises

1. Find all books priced below the average price of all books.
2. Find authors who have written at least one book with `price > 10`, using `EXISTS`.
3. Find authors who have never had a book published (assume some authors rows exist without matching books), using `NOT EXISTS`.
4. Using a subquery in `FROM`, find genres whose average price exceeds $9.

### Answers

```sql
-- 1
SELECT title, price FROM books
WHERE price < (SELECT AVG(price) FROM books);

-- 2
SELECT name FROM authors a
WHERE EXISTS (SELECT 1 FROM books b WHERE b.author_id = a.author_id AND b.price > 10);

-- 3
SELECT name FROM authors a
WHERE NOT EXISTS (SELECT 1 FROM books b WHERE b.author_id = a.author_id);

-- 4
SELECT genre, avg_price FROM (
    SELECT genre, AVG(price) AS avg_price FROM books GROUP BY genre
) sub
WHERE avg_price > 9;
```

