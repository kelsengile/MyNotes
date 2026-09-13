[Previous](./[2]-Data-Types-And-Table-Creation-In-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[4]-Indexes-And-Query-Planning-In-SQLite.md)

*Data And Querying*

# Lesson 3 - CRUD And Querying In SQLite

## 3.1 Inserting And Updating Data

```sql
INSERT INTO products (name, price) VALUES ('Keyboard', 49.99);

UPDATE products
SET price = 44.99
WHERE name = 'Keyboard';
```

SQLite also supports inserting several rows in one statement:

```sql
INSERT INTO products (name, price) VALUES
    ('Mouse', 19.99),
    ('Monitor', 149.99);
```

---

## 3.2 Selecting And Filtering

Standard `SELECT` queries work exactly as covered in Database Fundamentals:

```sql
SELECT name, price FROM products
WHERE price < 50
ORDER BY price DESC
LIMIT 10;
```

SQLite supports `LIMIT` (with optional `OFFSET`) for pagination, the same as MySQL and PostgreSQL.

---

## 3.3 UPSERT With ON CONFLICT

SQLite supports the SQL-standard `ON CONFLICT` clause (also known as **UPSERT**) to say what should happen when an `INSERT` would otherwise violate a `UNIQUE` or `PRIMARY KEY` constraint:

```sql
INSERT INTO products (id, name, price)
VALUES (1, 'Keyboard', 49.99)
ON CONFLICT (id) DO UPDATE SET price = excluded.price;
```

`excluded.price` refers to the value that *would* have been inserted, letting the conflicting row be updated with it instead of failing. An older, SQLite-specific shorthand — `INSERT OR REPLACE` — achieves a similar result by deleting the conflicting row and inserting a fresh one, which is simpler but loses any columns not explicitly listed.

---

## 3.4 SQLite-Specific Functions

SQLite ships with a compact but capable standard function library:

- `||` — the standard SQL string concatenation operator (unlike MySQL, which needs `CONCAT()`).
- `datetime('now')` — returns the current UTC date and time.
- `ifnull(value, default)` — returns `default` if `value` is `NULL`.
- `substr(string, start, length)` — extracts part of a string.

```sql
SELECT first_name || ' ' || last_name AS full_name
FROM users;
```

**🔍 Quick Example:** `SELECT datetime('now', '+1 day');` — SQLite's date functions accept "modifiers" like `+1 day` or `start of month`, letting you do date arithmetic directly in SQL without a separate `DATE_ADD`-style function.

[Previous](./[2]-Data-Types-And-Table-Creation-In-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[4]-Indexes-And-Query-Planning-In-SQLite.md)
