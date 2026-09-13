[Previous](./[2]-Data-Types-And-Table-Creation-In-MySQL.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[4]-Indexes-And-The-Query-Optimizer.md)

*Data And Querying*

# Lesson 3 - CRUD And Querying In MySQL

## 3.1 Inserting And Updating Data

```sql
INSERT INTO products (name, price) VALUES ('Keyboard', 49.99);

UPDATE products
SET price = 44.99
WHERE name = 'Keyboard';
```

MySQL also supports inserting multiple rows in a single statement, and an `INSERT ... ON DUPLICATE KEY UPDATE` shortcut that updates a row instead of failing if a unique key already exists:

```sql
INSERT INTO products (id, name, price) VALUES (1, 'Keyboard', 49.99)
ON DUPLICATE KEY UPDATE price = 49.99;
```

---

## 3.2 Selecting And Filtering

Standard `SELECT` queries work exactly as covered in Database Fundamentals:

```sql
SELECT name, price FROM products
WHERE price < 50
ORDER BY price DESC;
```

---

## 3.3 MySQL-Specific Functions

MySQL ships with a large standard function library, including some that differ from other databases:

- `CONCAT(a, b)` — joins strings together (MySQL has no `||` operator by default).
- `NOW()` — returns the current date and time.
- `IFNULL(value, default)` — returns `default` if `value` is `NULL`.
- `DATE_FORMAT(date, format)` — formats a date for display.

```sql
SELECT CONCAT(first_name, ' ', last_name) AS full_name
FROM users;
```

---

## 3.4 LIMIT And Pagination

MySQL uses `LIMIT` (and optionally `OFFSET`) to restrict how many rows a query returns — essential for paginating results in an application:

```sql
SELECT * FROM products
ORDER BY id
LIMIT 10 OFFSET 20;   -- rows 21–30
```

**🔍 Quick Example:** `LIMIT 10 OFFSET 20` is shorthand for "skip the first 20 matching rows, then return the next 10" — the pattern behind "page 3 of 10 results per page."

[Previous](./[2]-Data-Types-And-Table-Creation-In-MySQL.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[4]-Indexes-And-The-Query-Optimizer.md)