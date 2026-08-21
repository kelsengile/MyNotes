*Big Data & Tools*

# Lesson 43 - Working with SQL for Data Science

[Previous](./[42]-Introduction-to-Big-Data.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[44]-Data-Pipelines-and-ETL-Basics.md)

---

## 43.1 Why SQL Matters for Data Scientists

**SQL** (Structured Query Language) is the standard language for querying relational databases, and it remains one of the most important skills in data science — most company data lives in databases, and pulling exactly the data you need with SQL is often far more efficient than loading an entire table into Pandas first.

---

## 43.2 Core SQL Queries

```sql
SELECT customer_id, SUM(amount) AS total_spent
FROM orders
WHERE order_date >= '2026-01-01'
GROUP BY customer_id
HAVING SUM(amount) > 100
ORDER BY total_spent DESC
LIMIT 10;
```

- `WHERE` filters individual rows before grouping.
- `GROUP BY` groups rows for aggregation (similar to Pandas' `groupby`, Lesson 6).
- `HAVING` filters groups *after* aggregation (unlike `WHERE`, which can't reference aggregated values).
- `ORDER BY` and `LIMIT` sort and cap the number of returned rows.

---

## 43.3 Joins

```sql
SELECT o.order_id, c.name, o.amount
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id;

SELECT c.name, o.amount
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;
```

- **INNER JOIN** — keeps only rows with matches in both tables.
- **LEFT JOIN** — keeps all rows from the left table, filling in NULLs where there's no match.

These map directly to the `how="inner"` / `how="left"` options in Pandas' `merge()` function from Lesson 6.

---

## 43.4 Window Functions

**Window functions** perform calculations across a set of rows related to the current row, without collapsing them into a single group — useful for running totals, rankings, and comparisons to previous rows:

```sql
SELECT
  customer_id,
  order_date,
  amount,
  RANK() OVER (PARTITION BY customer_id ORDER BY amount DESC) AS spend_rank,
  SUM(amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS running_total
FROM orders;
```

Window functions are a powerful step beyond basic `GROUP BY` queries, letting you compute per-group calculations while still returning every individual row.

---

[Previous](./[42]-Introduction-to-Big-Data.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[44]-Data-Pipelines-and-ETL-Basics.md)
