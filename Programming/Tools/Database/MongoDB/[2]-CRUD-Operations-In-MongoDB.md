[Previous](./[1]-Getting-Started-With-MongoDB.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[3]-Querying-And-Filtering-In-Depth.md)

*Working With Documents*

# Lesson 2 - CRUD Operations In MongoDB

## 2.1 Inserting Documents

```javascript
db.products.insertOne({ name: "Keyboard", price: 49.99, inStock: true });

db.products.insertMany([
    { name: "Mouse", price: 19.99 },
    { name: "Monitor", price: 199.99 }
]);
```

If no `_id` field is provided, MongoDB automatically generates a unique `ObjectId` for it — this always acts as the document's primary key.

---

## 2.2 Querying With find()

```javascript
db.products.find({ price: { $lt: 50 } });

db.products.findOne({ name: "Keyboard" });
```

`find()` returns a **cursor** to every matching document; `findOne()` returns just the first match (or `null`). An empty filter `{}` matches every document in the collection.

---

## 2.3 Updating Documents

```javascript
db.products.updateOne(
    { name: "Keyboard" },
    { $set: { price: 44.99 } }
);

db.products.updateMany(
    { inStock: false },
    { $set: { onSale: true } }
);
```

Update operators like `$set` modify only the specified fields, leaving the rest of the document untouched — unlike a full document replacement.

---

## 2.4 Deleting Documents

```javascript
db.products.deleteOne({ name: "Keyboard" });

db.products.deleteMany({ inStock: false });
```

As with updates, `deleteOne` removes only the first match, while `deleteMany` removes every document matching the filter — an empty filter `{}` in `deleteMany` would remove the entire collection's contents, so it should be used carefully.

[Previous](./[1]-Getting-Started-With-MongoDB.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[3]-Querying-And-Filtering-In-Depth.md)