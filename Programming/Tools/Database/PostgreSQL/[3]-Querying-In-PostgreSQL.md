[Previous](./[2]-Data-Types-And-Table-Creation-In-PostgreSQL.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[4]-Indexes-And-Query-Planning.md)

*Data And Querying*

# Lesson 3 - Querying In PostgreSQL

## 3.1 SELECT And Filtering

Standard filtering works as covered in Database Fundamentals, with a few PostgreSQL conveniences like case-insensitive matching with `ILIKE`:

```sql
SELECT * FROM products
WHERE name ILIKE '%keyboard%'
ORDER BY price;
```

---

## 3.2 Joins And CTEs

Joins work the same way as standard SQL. PostgreSQL also supports **Common Table Expressions (CTEs)** using `WITH`, which let you name a subquery and reuse it, making complex queries far more readable:

```sql
WITH big_orders AS (
    SELECT customer_id, SUM(total) AS spent
    FROM orders
    GROUP BY customer_id
    HAVING SUM(total) > 500
)
SELECT customers.name, big_orders.spent
FROM customers
JOIN big_orders ON customers.id = big_orders.customer_id;
```

---

## 3.3 Window Functions

**Window functions** perform a calculation across a set of rows related to the current row, without collapsing them into a single result like `GROUP BY` does:

```sql
SELECT
    name,
    price,
    RANK() OVER (ORDER BY price DESC) AS price_rank
FROM products;
```

This ranks every product by price while still returning one row per product — something a plain aggregate query can't do.

---

## 3.4 Views And Materialized Views

A **view** is a saved query that behaves like a virtual table:

```sql
CREATE VIEW expensive_products AS
SELECT * FROM products WHERE price > 100;
```

A **materialized view** is similar, but its results are physically stored and must be manually refreshed — trading freshness for speed on expensive queries:

```sql
CREATE MATERIALIZED VIEW sales_summary AS
SELECT product_id, SUM(quantity) FROM order_items GROUP BY product_id;

REFRESH MATERIALIZED VIEW sales_summary;
```

[Previous](./[2]-Data-Types-And-Table-Creation-In-PostgreSQL.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[4]-Indexes-And-Query-Planning.md)