[Previous](./[4]-The-Aggregation-Framework.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[6]-Replication-Sharding-And-The-Ecosystem.md)

*Scaling MongoDB*

# Lesson 5 - Schema Design In MongoDB

## 5.1 Embedding vs Referencing

MongoDB offers two main ways to model related data:

- **Embedding** — nesting related data directly inside the parent document.
- **Referencing** — storing an `_id` that points to a document in another collection, similar to a foreign key.

```javascript
// Embedded
{ name: "Ana", address: { city: "Manila", zip: "1000" } }

// Referenced
{ name: "Ana", addressId: ObjectId("64f...") }
```

> 💡 **Rule of thumb:** Embed data that's almost always read together and doesn't grow without bound. Reference data that's shared across many documents, updated independently, or could grow very large (like a customer's entire order history).

---

## 5.2 Designing For Access Patterns

Unlike relational design, which starts from normalization rules, MongoDB schema design typically starts from **how the application will query the data**. A schema that's fast for one access pattern (e.g. "get a user and their 5 most recent orders") might be slow for a different one — so the read and write patterns of the application come first, and the document shape follows.

---

## 5.3 Schema Validation

Even though MongoDB doesn't require a fixed schema, it can still enforce one using **JSON Schema validation** on a collection:

```javascript
db.createCollection("products", {
    validator: {
        $jsonSchema: {
            required: ["name", "price"],
            properties: {
                name: { bsonType: "string" },
                price: { bsonType: "number", minimum: 0 }
            }
        }
    }
});
```

This gives teams a middle ground — flexible by default, but with guardrails where consistency matters most.

---

## 5.4 Common Anti-Patterns

- **Unbounded array growth** — embedding an array that grows forever (like every log entry a user ever generates) can hit MongoDB's 16MB document size limit and slow down every read of that document.
- **Excessive referencing** — modeling everything relationally out of habit throws away MongoDB's main advantage: fewer round trips through embedded data.
- **Massive fan-out `$lookup` chains** — relying on many joins across collections often signals the data would fit better in a relational database.

[Previous](./[4]-The-Aggregation-Framework.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md) | [Next](./[6]-Replication-Sharding-And-The-Ecosystem.md)