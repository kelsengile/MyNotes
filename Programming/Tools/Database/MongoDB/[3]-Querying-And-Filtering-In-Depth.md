[Previous](./[2]-CRUD-Operations-In-MongoDB.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[4]-The-Aggregation-Framework.md)

*Working With Documents*

# Lesson 3 - Querying And Filtering In Depth

## 3.1 Query Operators

MongoDB uses special `$`-prefixed **operators** inside a filter document instead of SQL-style comparison symbols:

| Operator | Meaning | Example |
|---|---|---|
| `$eq` | Equal to | `{ price: { $eq: 50 } }` |
| `$gt` / `$gte` | Greater than / or equal | `{ price: { $gt: 50 } }` |
| `$lt` / `$lte` | Less than / or equal | `{ price: { $lt: 50 } }` |
| `$in` | Matches any value in a list | `{ status: { $in: ["active", "pending"] } }` |
| `$and` / `$or` | Combine multiple conditions | `{ $or: [{ price: { $lt: 10 } }, { onSale: true }] }` |

---

## 3.2 Sorting And Projection

```javascript
db.products.find({ inStock: true })
    .sort({ price: -1 })   // -1 = descending, 1 = ascending
    .limit(10);

db.products.find({}, { name: 1, price: 1, _id: 0 });
```

The second argument to `find()` is a **projection** — it controls which fields are returned. `1` includes a field, `0` excludes it (`_id` is included by default unless explicitly excluded).

---

## 3.3 Working With Arrays And Nested Documents

MongoDB can query inside nested documents and arrays directly using dot notation:

```javascript
db.orders.insertOne({
    customer: { name: "Ana", email: "ana@example.com" },
    items: ["Keyboard", "Mouse"]
});

db.orders.find({ "customer.name": "Ana" });
db.orders.find({ items: "Mouse" });          // matches if array contains "Mouse"
db.orders.find({ items: { $all: ["Keyboard", "Mouse"] } });
```

---

## 3.4 Indexes In MongoDB

Just like relational databases, MongoDB uses indexes to avoid scanning every document in a collection:

```javascript
db.products.createIndex({ name: 1 });                 // single-field index
db.products.createIndex({ category: 1, price: -1 });  // compound index
db.products.createIndex({ name: "text" });             // text search index
```

Without an index, `find()` performs a **collection scan** — checking every single document, which gets slow as a collection grows into the millions of documents.

[Previous](./[2]-CRUD-Operations-In-MongoDB.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[4]-The-Aggregation-Framework.md)