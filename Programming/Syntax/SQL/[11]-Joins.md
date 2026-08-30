[Previous](./[10]-Set-Operations.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[12]-Subqueries.md)

# Lesson 11 - Joins — INNER, LEFT, RIGHT & FULL



---

Joins combine columns from two or more tables into a single result, based on a relationship between them (usually a key). This is the single most important skill in SQL — real databases split data across many tables (Lesson 15 explains why), and joins are how you bring it back together.

---

## 11.1 Sample setup

We'll extend the bookstore schema with an orders table:
```sql
CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    name TEXT
);

CREATE TABLE orders (
    order_id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    book_id INTEGER,
    order_date DATE
);

INSERT INTO customers VALUES
    (1, 'Amara'), (2, 'Beto'), (3, 'Chidi');

INSERT INTO orders VALUES
    (1, 1, 1, '2024-01-05'),
    (2, 1, 3, '2024-01-20'),
    (3, 2, 2, '2024-02-01'),
    (4, 4, 5, '2024-02-15');   -- note: customer_id 4 doesn't exist in customers!
```
Notice order 4 references a customer that doesn't exist — a realistic (if messy) scenario, useful for illustrating join behavior. (Lesson 13 shows how foreign keys prevent this from happening.)

---

## 11.2 INNER JOIN: only matching rows

`INNER JOIN` (often just written `JOIN`) returns rows where the join condition matches in *both* tables. Non-matching rows on either side are dropped.

```sql
SELECT o.order_id, c.name, o.order_date
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id;
```
Order 4 (customer_id 4) is **excluded** here, since there's no matching customer.

### Anatomy of a JOIN
```sql
SELECT columns
FROM table_a AS a
JOIN table_b AS b ON a.key = b.key;
```
- `AS a` / `AS b` are table aliases — shorthand names that make column references shorter and disambiguate columns that exist in both tables
- `ON` specifies the condition that links rows together

---

## 11.3 LEFT JOIN (LEFT OUTER JOIN): all rows from the left, matched or not

Returns every row from the left table, and matching data from the right table where it exists — filling in `NULL` where it doesn't.

```sql
SELECT o.order_id, c.name, o.order_date
FROM orders o
LEFT JOIN customers c ON o.customer_id = c.customer_id;
```
Now order 4 **is included**, but `c.name` is `NULL` for it, since there's no matching customer.

**Common use case:** finding rows in the left table with *no* match in the right table:
```sql
SELECT o.order_id, o.customer_id
FROM orders o
LEFT JOIN customers c ON o.customer_id = c.customer_id
WHERE c.customer_id IS NULL;
```
This finds "orphaned" orders — a very common real-world data integrity check.

---

## 11.4 RIGHT JOIN (RIGHT OUTER JOIN): all rows from the right, matched or not

The mirror image of `LEFT JOIN` — every row from the right table, matched data from the left where available:
```sql
SELECT c.name, o.order_id
FROM orders o
RIGHT JOIN customers c ON o.customer_id = c.customer_id;
```
This includes Chidi (customer_id 3), who has no orders — `o.order_id` is `NULL` for that row.

**Dialect note:** `RIGHT JOIN` is supported by PostgreSQL, MySQL, and SQL Server. **SQLite did not support `RIGHT JOIN` until version 3.39 (2022)** — in older SQLite, you'd rewrite it as a `LEFT JOIN` by swapping the table order.

In practice, most people avoid `RIGHT JOIN` entirely and just swap table order to use `LEFT JOIN` instead, since it reads more naturally.

---

## 11.5 FULL JOIN (FULL OUTER JOIN): everything, matched or not

Returns every row from both tables — matched where possible, `NULL`-filled where not.
```sql
SELECT c.name, o.order_id
FROM customers c
FULL JOIN orders o ON c.customer_id = o.customer_id;
```
This shows Chidi with no order, and order 4 with no customer, in the same result set.

**Dialect note:** PostgreSQL and SQL Server support `FULL JOIN` directly. **MySQL and SQLite do not** — you simulate it by combining a `LEFT JOIN` and a `RIGHT JOIN` (or two `LEFT JOIN`s) with `UNION`:
```sql
SELECT c.name, o.order_id FROM customers c LEFT JOIN orders o ON c.customer_id = o.customer_id
UNION
SELECT c.name, o.order_id FROM customers c RIGHT JOIN orders o ON c.customer_id = o.customer_id;
```

---

## 11.6 Visual summary

```
INNER JOIN:  only the overlap
LEFT JOIN:   everything on the left, plus the overlap
RIGHT JOIN:  everything on the right, plus the overlap
FULL JOIN:   everything from both sides
```

---

## 11.7 Joining more than two tables

Chain joins together — each new `JOIN` can reference any table already brought into the query:
```sql
SELECT c.name, b.title, o.order_date
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN books b ON o.book_id = b.book_id;
```

---

## 11.8 CROSS JOIN: every combination

Returns the Cartesian product — every row from table A paired with every row from table B, with no matching condition:
```sql
SELECT c.name, b.title FROM customers c CROSS JOIN books b;
```
If `customers` has 3 rows and `books` has 6, this returns 18 rows. Rarely used directly, except for generating combinations (e.g., all possible size/color pairs for a product).

---

## 11.9 Self joins

A table can be joined to itself — useful for hierarchical or comparative data, like finding employees managed by the same person:
```sql
SELECT e1.name AS employee, e2.name AS manager
FROM employees e1
JOIN employees e2 ON e1.manager_id = e2.employee_id;
```

---

[Previous](./[10]-Set-Operations.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[12]-Subqueries.md)
