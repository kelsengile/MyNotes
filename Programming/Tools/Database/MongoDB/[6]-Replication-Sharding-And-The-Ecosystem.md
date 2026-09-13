[Previous](./[5]-Schema-Design-In-MongoDB.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md)

*Scaling MongoDB*

# Lesson 6 - Replication, Sharding, And The Ecosystem

## 6.1 Replica Sets

A **replica set** is a group of MongoDB servers holding copies of the same data. One member is elected the **primary**, handling all writes, while **secondary** members replicate its data and can serve reads. If the primary fails, the remaining members automatically elect a new primary — providing built-in high availability without extra tooling.

---

## 6.2 Sharding Basics

**Sharding** is MongoDB's horizontal scaling strategy (see Database Fundamentals, Lesson 18) — it splits a collection's data across multiple servers based on a **shard key**, so no single server has to hold or process the entire dataset.

```javascript
sh.shardCollection("shop.orders", { customerId: 1 });
```

Choosing a good shard key is critical: a poor choice (like a field that's always incrementing) can concentrate all writes on a single shard, defeating the purpose of sharding entirely.

---

## 6.3 MongoDB Atlas

**MongoDB Atlas** is MongoDB's official fully-managed cloud database service. It handles provisioning, replication, backups, and scaling automatically, and offers a free tier — making it a common way for beginners and production teams alike to run MongoDB without managing servers directly.

---

## 6.4 When To Choose MongoDB

MongoDB is a strong choice when:

- Your data has a naturally nested or variable shape (user profiles, product catalogs with different attributes per category, content management).
- Your application's schema is expected to evolve frequently during development.
- You need to scale horizontally across many servers with built-in sharding.

Consider a relational database like **PostgreSQL** or **MySQL** instead when your data is highly structured, relationships between entities are central to your queries, or you need strict multi-table transactional guarantees.

[Previous](./[5]-Schema-Design-In-MongoDB.md) | [Table of Contents](./[0]-Introduction-to-MongoDB.md)