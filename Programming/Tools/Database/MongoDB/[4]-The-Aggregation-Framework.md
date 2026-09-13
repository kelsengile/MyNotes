[Previous](./[3]-Querying-And-Filtering-In-Depth.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[5]-Schema-Design-In-MongoDB.md)

*Working With Documents*

# Lesson 4 - The Aggregation Framework

## 4.1 What Is An Aggregation Pipeline

The **aggregation pipeline** is MongoDB's tool for transforming and summarizing data — the rough equivalent of SQL's `GROUP BY`, `JOIN`, and computed columns combined. Data flows through an ordered series of **stages**, each one transforming the documents before passing them to the next.

```javascript
db.orders.aggregate([
    { $match: { status: "completed" } },
    { $group: { _id: "$customerId", total: { $sum: "$amount" } } },
    { $sort: { total: -1 } }
]);
```

---

## 4.2 Common Stages ($match, $group, $project)

| Stage | Purpose |
|---|---|
| `$match` | Filters documents — like a `WHERE` clause |
| `$group` | Groups documents and computes aggregates (`$sum`, `$avg`, `$count`) |
| `$project` | Reshapes documents — include, exclude, or compute new fields |
| `$sort` | Orders documents |
| `$limit` | Restricts how many documents pass through |

```javascript
db.products.aggregate([
    { $match: { inStock: true } },
    { $project: { name: 1, priceWithTax: { $multiply: ["$price", 1.08] } } }
]);
```

---

## 4.3 Lookups (Joins In MongoDB)

MongoDB is document-oriented, but the `$lookup` stage can join data from another collection when needed — the closest equivalent to a SQL `JOIN`:

```javascript
db.orders.aggregate([
    {
        $lookup: {
            from: "customers",
            localField: "customerId",
            foreignField: "_id",
            as: "customerInfo"
        }
    }
]);
```

This attaches matching documents from the `customers` collection onto each order as a `customerInfo` array field.

---

## 4.4 When To Use Aggregation vs Queries

- Use plain `find()` for simple lookups and filtering — it's simpler and generally faster.
- Reach for the **aggregation pipeline** when you need to group, summarize, reshape, or combine data across collections — anything a single `find()` query can't express.

[Previous](./[3]-Querying-And-Filtering-In-Depth.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[5]-Schema-Design-In-MongoDB.md)