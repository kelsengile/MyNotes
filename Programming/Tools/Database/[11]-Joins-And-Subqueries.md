[Previous](./[10]-Filtering-Sorting-And-Aggregating.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[12]-Transactions-And-ACID.md)

*SQL And Querying*

# Lesson 11 - Joins And Subqueries

## 11.1 Why Joins Are Needed

Because normalized data is split across multiple tables (see [Lesson 7](./[7]-Normalization.md)), answering many real questions requires combining rows from more than one table at once. A **join** does exactly this — it matches rows from two (or more) tables based on a related column, usually a foreign key.

---

## 11.2 INNER JOIN

An `INNER JOIN` returns only the rows that have a match in **both** tables. If a user has no orders, that user won't appear in the results at all:

```sql
SELECT users.first_name, orders.total
FROM users
INNER JOIN orders ON users.id = orders.user_id;
```

This returns one row per order, paired with the name of the user who placed it — users with zero orders are excluded.

---

## 11.3 OUTER JOINs (LEFT/RIGHT/FULL)

**Outer joins** include rows even when there's no match on the other side, filling in `NULL` for the missing columns:

- `LEFT JOIN` — keeps every row from the left (first) table, matching rows from the right table where they exist.
- `RIGHT JOIN` — the mirror image: keeps every row from the right table.
- `FULL JOIN` — keeps every row from both tables, matched where possible.

```sql
SELECT users.first_name, orders.total
FROM users
LEFT JOIN orders ON users.id = orders.user_id;
```

This returns every user, including those with no orders — their `total` column shows `NULL`.

---

## 11.4 Subqueries

A **subquery** is a query nested inside another query, used when a result needs to be computed before the outer query can filter or select against it:

```sql
SELECT first_name
FROM users
WHERE id IN (
    SELECT user_id FROM orders WHERE total > 100
);
```

The inner query finds the IDs of users with at least one order over 100; the outer query then finds those users' names. Subqueries and joins often solve overlapping problems — joins are usually more efficient for combining rows, while subqueries can be clearer for existence checks or computed comparisons.

[Previous](./[10]-Filtering-Sorting-And-Aggregating.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[12]-Transactions-And-ACID.md)
