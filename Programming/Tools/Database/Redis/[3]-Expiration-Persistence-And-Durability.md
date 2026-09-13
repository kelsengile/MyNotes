[Previous](./[2]-Core-Redis-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[4]-PubSub-And-Messaging-Patterns.md)

*Data Structures And Commands*

# Lesson 3 - Expiration, Persistence, And Durability

## 3.1 TTL And EXPIRE

Any Redis key can be given an expiration time, after which it's automatically deleted — extremely useful for caching, sessions, and rate limiting:

```bash
SET session:abc123 "user_1" EX 3600   # expires in 3600 seconds (1 hour)
TTL session:abc123                     # seconds remaining, or -1 if no expiry
PERSIST session:abc123                 # removes the expiration
```

---

## 3.2 RDB Snapshots

Even though Redis is an in-memory store, it can still write its data to disk so it isn't lost on restart. **RDB (Redis Database)** persistence periodically saves a full point-in-time snapshot of the dataset to a `.rdb` file:

```
save 900 1      # save if at least 1 key changed in 900 seconds
save 60 10000   # save if at least 10,000 keys changed in 60 seconds
```

RDB produces compact, fast-to-load files, but any writes since the last snapshot are lost if Redis crashes.

---

## 3.3 AOF Logging

**AOF (Append Only File)** persistence takes a different approach: it logs every write command as it happens, and replays that log to rebuild the dataset on restart. This offers much stronger durability than RDB, at the cost of larger file sizes and slightly slower writes.

```
appendonly yes
appendfsync everysec   # fsync to disk roughly once per second
```

---

## 3.4 Choosing A Persistence Strategy

| Strategy | Durability | Performance | Best For |
|---|---|---|---|
| **None** | None — data lost on restart | Fastest | Pure caching, disposable data |
| **RDB only** | Point-in-time (can lose recent writes) | Fast | Backups, acceptable minor data loss |
| **AOF only** | Very strong | Slightly slower writes | Data that must survive a crash |
| **RDB + AOF** | Strongest | Balanced | Most production deployments |

[Previous](./[2]-Core-Redis-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[4]-PubSub-And-Messaging-Patterns.md)