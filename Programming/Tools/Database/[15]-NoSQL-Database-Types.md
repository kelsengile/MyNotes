[Previous](./[14]-Concurrency-And-Locking.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[16]-CAP-Theorem-And-Consistency-Models.md)

*Beyond Relational*

# Lesson 15 - NoSQL Database Types

## 15.1 Document Databases

**Document databases** store data as self-contained documents, usually in a JSON-like format, rather than as rows spread across normalized tables (see [7.1](./[7]-Normalization.md)). Each document can have a different shape, which makes this model flexible for data that doesn't fit neatly into a fixed schema.

```json
{
  "_id": "u001",
  "name": "Ana Cruz",
  "email": "ana@example.com",
  "orders": [
    { "product": "Keyboard", "price": 49.99 },
    { "product": "Mouse", "price": 19.99 }
  ]
}
```

Notice that the user's orders live directly inside their document, instead of being split into a separate `orders` table joined by a foreign key (see [6.2](./[6]-Keys-And-Relationships.md)). Popular document databases include MongoDB and Couchbase.

---

## 15.2 Key-Value Stores

A **key-value store** is the simplest NoSQL model: every piece of data is a value accessed by a unique key, similar to a giant dictionary or hash map. There's no query language for searching inside the value — you simply fetch it by its key.

```
SET session:abc123 "{ userId: 42, expires: 1699999999 }"
GET session:abc123
```

This simplicity makes key-value stores extremely fast, which is why they're commonly used for caching, session storage, and real-time lookups. Popular key-value stores include Redis and Amazon DynamoDB.

---

## 15.3 Column-Family Databases

**Column-family databases** organize data into rows, but each row can have a different set of columns, and columns are grouped into "families" that are stored together on disk. This differs from a relational table, where every row shares the exact same fixed set of columns (see [4.1](./[4]-Tables-Rows-And-Columns.md)).

This model is optimized for writing and reading huge volumes of data where queries typically only need a few columns at once — for example, storing time-series sensor readings where each device might report different metrics. Popular column-family databases include Apache Cassandra and HBase.

---

## 15.4 Graph Databases

**Graph databases** store data as **nodes** (entities) and **edges** (the relationships between them), making the relationships themselves first-class data rather than something reconstructed with foreign keys and joins (see [11.1](./[11]-Joins-And-Subqueries.md)).

```
(Ana)-[:FOLLOWS]->(Ben)
(Ben)-[:FOLLOWS]->(Cleo)
(Ana)-[:LIKES]->(Post 1)
```

This model shines for highly interconnected data where the relationships matter as much as the entities themselves — social networks, recommendation engines, and fraud detection are common use cases, since questions like "friends of friends who liked this post" are fast and natural to express. Popular graph databases include Neo4j and Amazon Neptune.

[Previous](./[14]-Concurrency-And-Locking.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[16]-CAP-Theorem-And-Consistency-Models.md)
