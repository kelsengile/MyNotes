[Previous](./[14]-Managed-Relational-Databases.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[16]-Caching-Services.md)

*Databases*

# Lesson 15 - Managed NoSQL Databases

## 15.1 NoSQL Data Models

**NoSQL** databases store data in models other than the relational (tables/rows) model, generally trading strict consistency and complex joins for flexible schemas and horizontal scalability. Common NoSQL data models:

- **Key-value** — data stored and retrieved by a simple key (e.g. Redis, DynamoDB in simple mode).
- **Document** — semi-structured documents (usually JSON), where fields can vary between records (e.g. MongoDB).
- **Wide-column** — rows can have different columns, optimized for huge datasets (e.g. Cassandra).
- **Graph** — data modeled as nodes and relationships, for highly connected data (e.g. Neo4j).

NoSQL databases generally scale horizontally (adding more machines) more easily than traditional relational databases, which is why they're common for very large or rapidly growing datasets.

---

## 15.2 Popular Managed NoSQL Services

- **AWS DynamoDB** — a fully managed key-value/document database, known for single-digit-millisecond performance at massive scale, with a fully serverless pricing model.
- **Azure Cosmos DB** — a globally distributed, multi-model database supporting document, key-value, graph, and column-family APIs.
- **Google Firestore/Bigtable** — Firestore for document data (often paired with mobile/web apps), Bigtable for large-scale wide-column workloads.

Like managed relational databases, these handle scaling, replication, and availability automatically, letting developers focus on application logic instead of database operations.

---

## 15.3 When to Choose NoSQL

NoSQL is generally a good fit when: your data doesn't naturally fit rigid tables (nested, varying-shape records), you need to scale writes/reads horizontally across many machines, low and predictable latency at large scale matters more than complex relational queries, or your access patterns are simple and known in advance (NoSQL databases are typically designed around specific query patterns rather than ad-hoc joins). Relational databases (Lesson 14) remain the better choice when you need strong consistency, complex multi-table queries, and transactional guarantees across related data.

---

[Previous](./[14]-Managed-Relational-Databases.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[16]-Caching-Services.md)
