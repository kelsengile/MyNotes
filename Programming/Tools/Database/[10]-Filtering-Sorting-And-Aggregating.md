[Previous](./[9]-CRUD-Operations.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[11]-Joins-And-Subqueries.md)

*SQL And Querying*

# Lesson 10 - Filtering, Sorting, And Aggregating

## 10.1 WHERE Clauses

The `WHERE` clause filters which rows a query returns, based on a condition. It supports comparison operators (`=`, `!=`, `>`, `<`, `>=`, `<=`), logical operators (`AND`, `OR`, `NOT`), and pattern matching:

```sql
SELECT * FROM users
WHERE created_at > '2026-01-01' AND email LIKE '%@example.com';
```

`LIKE` performs pattern matching, where `%` matches any sequence of characters — here, matching any email ending in `@example.com`.

---

## 10.2 ORDER BY and LIMIT

`ORDER BY` sorts the result set by one or more columns, either ascending (`ASC`, the default) or descending (`DESC`). `LIMIT` restricts how many rows are returned:

```sql
SELECT first_name, created_at
FROM users
ORDER BY created_at DESC
LIMIT 5;
```

This returns the five most recently created users.

---

## 10.3 Aggregate Functions

**Aggregate functions** compute a single value from many rows. The most common ones:

- `COUNT()` — number of rows.
- `SUM()` — total of a numeric column.
- `AVG()` — average of a numeric column.
- `MIN()` / `MAX()` — smallest / largest value.

```sql
SELECT COUNT(*) FROM users;
```

This returns the total number of users in the table.

---

## 10.4 GROUP BY and HAVING

`GROUP BY` splits rows into groups sharing a common value, so an aggregate function can be applied to each group separately rather than the whole table:

```sql
SELECT country, COUNT(*) AS user_count
FROM users
GROUP BY country;
```

This returns the number of users per country. `HAVING` then filters *groups* after aggregation — unlike `WHERE`, which filters individual rows before grouping:

```sql
SELECT country, COUNT(*) AS user_count
FROM users
GROUP BY country
HAVING COUNT(*) > 100;
```

This returns only countries with more than 100 users.

[Previous](./[9]-CRUD-Operations.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[11]-Joins-And-Subqueries.md)
