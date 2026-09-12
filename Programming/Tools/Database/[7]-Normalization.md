[Previous](./[6]-Keys-And-Relationships.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[8]-Introduction-To-SQL.md)

*Relational Design*

# Lesson 7 - Normalization

## 7.1 Why Normalize

**Normalization** is the process of organizing tables to reduce data duplication and prevent inconsistent data. Without it, the same fact can end up stored in multiple places — for example, a customer's address repeated on every one of their orders. If that address changes, every duplicate copy needs to be updated, and missing even one creates a database with conflicting answers to the same question.

Normalization fixes this by splitting data into well-structured tables connected by foreign keys, so each fact is stored in exactly one place.

> 💡 **Analogy:** Imagine writing your home address on every single letter in a box of correspondence, instead of just having one address book entry that every letter can reference. Move house, and you'd have to hunt down and correct every single letter — normalization keeps the address in one place.

---

## 7.2 First Normal Form (1NF)

A table is in **First Normal Form** when:

- Each column holds a single, indivisible value (no comma-separated lists crammed into one field).
- Each row is uniquely identifiable (typically via a primary key).

For example, a `phone_numbers` column containing `"555-1234, 555-5678"` violates 1NF — it should be split into a separate table with one phone number per row.

---

## 7.3 Second Normal Form (2NF)

A table is in **Second Normal Form** when it is already in 1NF, and every non-key column depends on the *entire* primary key — not just part of it. This only matters for tables with a **composite** primary key (made of more than one column).

For example, in an `order_items` table keyed on `(order_id, product_id)`, a column like `product_name` depends only on `product_id`, not on the full key — that's a 2NF violation, and `product_name` belongs in a separate `products` table instead.

---

## 7.4 Third Normal Form (3NF)

A table is in **Third Normal Form** when it is already in 2NF, and every non-key column depends only on the primary key — not on another non-key column. For example, if an `orders` table stores both `customer_id` and `customer_email`, the email depends on the customer, not directly on the order — that's a 3NF violation, and the email belongs in the `customers` table.

Most well-designed relational schemas aim for at least 3NF, which is normalized enough to eliminate the vast majority of duplication and update anomalies.

```
1NF: no comma-separated lists, every row uniquely identifiable
  ↓
2NF: 1NF + non-key columns depend on the WHOLE composite key
  ↓
3NF: 2NF + non-key columns depend ONLY on the key, not on each other
```

---

## 7.5 Denormalization Trade-offs

**Denormalization** means intentionally reintroducing some duplication, usually to improve read performance. Fully normalized schemas often require many joins (see [Lesson 11](./[11]-Joins-And-Subqueries.md)) to reassemble a complete picture of an entity, which can be slow at very high read volumes.

Denormalization is a deliberate trade-off: faster reads in exchange for more complex writes and a higher risk of inconsistent data. It's typically applied selectively, after identifying a specific performance problem — not as a default starting point.

**🔍 Quick Example:** A news site might denormalize by storing an article's `author_name` directly on the article, instead of joining to an `authors` table every single time the article is displayed to a reader — trading a small risk of stale names for much faster page loads.

[Previous](./[6]-Keys-And-Relationships.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[8]-Introduction-To-SQL.md)
