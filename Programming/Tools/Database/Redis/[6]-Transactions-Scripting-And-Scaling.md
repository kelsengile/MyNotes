[Previous](./[5]-Redis-As-A-Cache.md) | [Table of Contents](./[0]-Introduction-to-Redis.md)

*Redis In Production*

# Lesson 6 - Transactions, Scripting, And Scaling

## 6.1 MULTI/EXEC Transactions

Redis supports basic transactions by queuing up a group of commands and executing them all at once, with no other client's commands interleaved in between:

```bash
MULTI
SET account:1:balance 400
SET account:2:balance 600
EXEC
```

Unlike relational transactions, Redis transactions don't support rolling back individual failed commands mid-batch — they guarantee **isolation** (nothing else runs in between), not automatic error recovery.

---

## 6.2 Lua Scripting

For more complex atomic operations, Redis can execute **Lua scripts** directly on the server, guaranteeing the entire script runs as a single atomic step:

```bash
EVAL "return redis.call('GET', KEYS[1])" 1 user:1:name
```

This is especially useful for "check-then-act" logic (like "only decrement stock if it's above zero") that would otherwise require multiple round trips and risk race conditions.

---

## 6.3 Replication And Redis Cluster

- **Replication** — a primary Redis instance can replicate its data to one or more read replicas, improving read throughput and providing a failover target.
- **Redis Sentinel** — monitors primary/replica instances and handles automatic failover if the primary goes down.
- **Redis Cluster** — shards data automatically across multiple nodes, letting a dataset scale beyond what a single server's memory could hold.

---

## 6.4 When To Choose Redis

Redis is a strong choice when:

- You need a caching layer to take load off a slower primary database.
- You need extremely fast counters, rate limiters, or leaderboards.
- You need lightweight pub/sub or stream-based messaging alongside your main data store.

Redis is rarely used as an application's *only* database — pair it with a relational database like **PostgreSQL** or a document database like **MongoDB** for data that needs to persist reliably and be queried in complex ways.

[Previous](./[5]-Redis-As-A-Cache.md) | [Table of Contents](./[0]-Introduction-to-Redis.md)