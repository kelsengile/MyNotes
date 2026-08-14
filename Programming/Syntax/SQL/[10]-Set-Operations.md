[Previous](./[9]-Aggregate-Functions-and-Group-By.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[11]-Joins.md)

# Lesson 10 - Set Operations — UNION, INTERSECT & EXCEPT



---

Set operations combine the *results* of two separate `SELECT` queries, stacking or comparing rows rather than combining columns (that's what joins do — Lesson 11).

---

## The requirement: compatible columns

For any set operation, both queries must return:
- The same number of columns
- Columns in a compatible data type order (types don't need to match exactly, but need to be comparable)

The column names in the final result come from the **first** query.

---

## Sample setup

```sql
CREATE TABLE fiction_bestsellers (title TEXT, year INTEGER);
INSERT INTO fiction_bestsellers VALUES
    ('Kafka on the Shore', 2002),
    ('Half of a Yellow Sun', 2006),
    ('Norwegian Wood', 1987);

CREATE TABLE award_winners (title TEXT, year INTEGER);
INSERT INTO award_winners VALUES
    ('Half of a Yellow Sun', 2006),
    ('The Left Hand of Darkness', 1969),
    ('Norwegian Wood', 1987);
```

---

## UNION: combine rows, remove duplicates

```sql
SELECT title, year FROM fiction_bestsellers
UNION
SELECT title, year FROM award_winners;
```
Returns every distinct row that appears in *either* query. Because `UNION` removes duplicates, it does extra work sorting/de-duplicating — if you know there won't be duplicates, or don't care, `UNION ALL` is faster.

---

## UNION ALL: combine rows, keep duplicates

```sql
SELECT title, year FROM fiction_bestsellers
UNION ALL
SELECT title, year FROM award_winners;
```
This returns every row from both queries, including 'Norwegian Wood' and 'Half of a Yellow Sun' *twice* since they appear in both tables. `UNION ALL` is almost always faster than `UNION` and should be your default whenever duplicates are acceptable or impossible.

---

## INTERSECT: only rows in both

```sql
SELECT title, year FROM fiction_bestsellers
INTERSECT
SELECT title, year FROM award_winners;
```
Returns only rows that appear in *both* result sets — here, 'Half of a Yellow Sun' and 'Norwegian Wood'.

**Dialect note:** `INTERSECT` is supported in PostgreSQL, SQLite, and SQL Server. MySQL only added `INTERSECT` in version 8.0.31+ — in older MySQL, you'd simulate it with an `INNER JOIN` or `WHERE ... IN (subquery)` instead.

---

## EXCEPT (aka MINUS): rows in the first, not the second

```sql
SELECT title, year FROM fiction_bestsellers
EXCEPT
SELECT title, year FROM award_winners;
```
Returns rows from the first query that *don't* appear in the second — here, just 'Kafka on the Shore'.

**Dialect note:** PostgreSQL, SQLite, and SQL Server use `EXCEPT`. Oracle uses `MINUS` (same behavior, different keyword). MySQL didn't support either until 8.0.31+.

---

## Ordering the combined result

`ORDER BY` applies to the *entire combined result*, and goes at the very end (only one `ORDER BY` is allowed per set operation):
```sql
SELECT title, year FROM fiction_bestsellers
UNION
SELECT title, year FROM award_winners
ORDER BY year;
```

---

## A practical use: combining data from similarly-shaped tables

Set operations are especially useful when the same kind of data is split across tables — for example, current-year and archived records:
```sql
SELECT customer_id, name FROM customers
UNION ALL
SELECT customer_id, name FROM archived_customers;
```

---

## Set operations vs JOIN: when to use which

- Use a **JOIN** when you want to combine *columns* from two related tables into wider rows (e.g., books with their author's name attached).
- Use a **set operation** when you want to combine or compare *rows* from two queries with the same shape (e.g., "titles that are bestsellers OR award winners").

---

[Previous](./[9]-Aggregate-Functions-and-Group-By.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[11]-Joins.md)
