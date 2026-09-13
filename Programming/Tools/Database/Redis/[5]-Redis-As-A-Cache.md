[Previous](./[4]-PubSub-And-Messaging-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[6]-Transactions-Scripting-And-Scaling.md)

*Redis In Production*

# Lesson 5 - Redis As A Cache

## 5.1 Common Caching Patterns

The most common use of Redis is as a **cache** sitting in front of a slower primary database. The most widely used pattern is **cache-aside** (also called lazy loading):

1. Application checks Redis for the data.
2. **Cache hit** — data is returned immediately from Redis.
3. **Cache miss** — application queries the primary database, then stores the result in Redis for next time.

```bash
GET product:42
# miss → query the database, then:
SET product:42 "{...serialized product...}" EX 300
```

---

## 5.2 Eviction Policies

Since Redis's memory is finite, it needs a strategy for what to do when it runs out of space. This is controlled by the `maxmemory-policy` setting:

| Policy | Behavior |
|---|---|
| `noeviction` | Reject new writes once memory is full |
| `allkeys-lru` | Evict the least recently used key, from any key |
| `volatile-lru` | Evict the least recently used key, but only among keys with a TTL set |
| `allkeys-random` | Evict a random key |

`allkeys-lru` is a common default for pure caching workloads.

---

## 5.3 Cache Invalidation Strategies

- **TTL-based expiration** — simplest approach; cached data automatically goes stale and is refetched after a set time.
- **Write-through invalidation** — the application explicitly deletes or updates the cached key whenever the underlying data changes.
- **Versioned keys** — including a version number in the key itself (e.g. `product:42:v3`) so old cached versions are simply never looked up again.

---

## 5.4 Avoiding Cache Stampedes

A **cache stampede** happens when a popular cached key expires, and a sudden flood of requests all miss the cache at once and hit the primary database simultaneously — potentially overwhelming it. Common mitigations:

- **Staggered expiration** — add a small random offset to TTLs so keys don't all expire at exactly the same moment.
- **Locking** — the first request to miss the cache acquires a short lock and repopulates it, while other requests wait briefly instead of all querying the database at once.

[Previous](./[4]-PubSub-And-Messaging-Patterns.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[6]-Transactions-Scripting-And-Scaling.md)