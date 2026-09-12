[Previous](./[17]-Database-Administration-Basics.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[19]-Choosing-A-Database-Tool.md)

*Operations And Next Steps*

# Lesson 18 - Scaling Databases

## 18.1 Vertical vs Horizontal Scaling

As an application grows, its database needs to handle more data and more traffic. There are two fundamental ways to scale:

- **Vertical scaling (scaling up)** — giving the existing database server more resources: more CPU, more RAM, faster disks. It's simple (no application changes needed) but has a ceiling — eventually you run out of bigger hardware to buy, and a single machine is still a single point of failure.
- **Horizontal scaling (scaling out)** — spreading the database across multiple machines instead of one. This has no hard ceiling and improves fault tolerance, but requires more architectural complexity, since data now lives in more than one place.

Most large-scale systems eventually rely on horizontal scaling, using techniques like replication and sharding below.

> 💡 **Analogy:** Vertical scaling is hiring one increasingly superhuman employee to do all the work faster. Horizontal scaling is hiring a whole team instead — more total capacity, but now you need to coordinate who does what.

---

## 18.2 Replication

**Replication** means keeping copies of the same data on multiple database servers, called **replicas**. A common pattern is **primary-replica replication**: all writes go to a single primary server, which then propagates those changes to one or more read-only replicas.

```
        Writes
          │
          ▼
      [ Primary ]
       │       │
   replicates  replicates
       ▼       ▼
  [Replica A] [Replica B]  ← Reads
```

This spreads read traffic across multiple servers (great for read-heavy applications) and provides a backup if the primary fails. The trade-off is that replicas may lag slightly behind the primary, which connects back to the consistency trade-offs discussed in [16.3](./[16]-CAP-Theorem-And-Consistency-Models.md).

---

## 18.3 Sharding and Partitioning

When a single server can't hold all the data even with better hardware, the data itself can be split up:

- **Partitioning** — splitting one large table into smaller pieces, typically still on the same server, based on a rule like date ranges or ID ranges.
- **Sharding** — partitioning taken further across multiple *separate* database servers, where each shard holds a subset of the data (for example, users A–M on one server, N–Z on another).

Sharding lets a system scale writes as well as reads, since different shards can accept writes independently. The trade-off is complexity: queries that need data from multiple shards (like joins across shard boundaries) become much harder to write and slower to run.

> 💡 **Analogy:** Sharding is like splitting one giant library into several branch libraries by last name — "A–M" at one branch, "N–Z" at another. Finding a book is fast if you know which branch to check, but a search across "every book by any author" now means checking every branch.

---

## 18.4 Caching Layers

A **cache** stores the results of expensive or frequently-repeated queries in fast, temporary storage (often in-memory, like a key-value store — see [15.2](./[15]-NoSQL-Database-Types.md)), so the database doesn't have to redo the same work repeatedly.

```
Request → Check cache
             │
     Hit ────┴──── Miss
      │              │
  Return cached   Query database,
     value        then store result
                     in cache
```

A well-placed cache can dramatically reduce load on the database and improve response times, but it introduces a new problem: **cache invalidation** — making sure the cache is updated or cleared when the underlying data changes, so users don't see stale information indefinitely.

**🔍 Quick Example:** A product page might cache its price for 60 seconds. If the price changes mid-cache, some shoppers briefly see the old price — a small, deliberate trade-off for not hitting the database on every single page view.

[Previous](./[17]-Database-Administration-Basics.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[19]-Choosing-A-Database-Tool.md)
