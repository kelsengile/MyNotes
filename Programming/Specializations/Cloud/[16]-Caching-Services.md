[Previous](./[15]-Managed-NoSQL-Databases.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[17]-DNS-and-Domain-Management.md)

*Databases*

# Lesson 16 - Caching Services (Redis/Memcached)

## 16.1 Why Caching?

A **cache** is an in-memory data store that holds frequently accessed data so it can be read far faster than fetching it from a disk-based database on every request. Because memory access is orders of magnitude faster than disk access, inserting a cache between your application and your database reduces latency for end users and reduces load on the database itself — a common target for expensive or repeated queries (e.g. a product catalog page, a user's session data, a leaderboard).

---

## 16.2 Redis vs Memcached

The two dominant caching engines, both offered as managed services (AWS ElastiCache, Azure Cache for Redis, Google Memorystore):

- **Memcached** — a simple, multi-threaded key-value store. Very fast for pure caching, but data is not persisted and there's no support for complex data structures.
- **Redis** — also a key-value store, but supports richer data structures (lists, sets, sorted sets, hashes), optional persistence to disk, pub/sub messaging, and replication. Redis is the more feature-rich and widely used choice today, though Memcached can be simpler and faster for pure, high-volume key-value caching.

---

## 16.3 Caching Strategies

Common patterns for using a cache alongside a database:

- **Cache-aside (lazy loading)** — the application checks the cache first; on a miss, it reads from the database and writes the result into the cache for next time.
- **Write-through** — every write goes to the cache and the database at the same time, keeping them in sync.
- **TTL (time-to-live)** — cached entries automatically expire after a set duration, balancing freshness against cache hit rate.

A key challenge with caching is **cache invalidation** — ensuring stale data is removed or updated when the underlying data changes, since serving outdated cached data can be worse than not caching at all.

---

[Previous](./[15]-Managed-NoSQL-Databases.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[17]-DNS-and-Domain-Management.md)
