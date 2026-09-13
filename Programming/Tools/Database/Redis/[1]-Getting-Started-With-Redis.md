[Previous](./[0]-Introduction-to-Redis.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[2]-Core-Redis-Data-Types.md)

*Getting Started*

# Lesson 1 - Getting Started With Redis

## 1.1 What Is Redis

**Redis** ("REmote DIctionary Server") is an open-source, in-memory **key-value store** (see Database Fundamentals, Lesson 15). Instead of tables or documents, Redis stores data as a giant dictionary of keys mapped to values, and because that data lives in RAM instead of on disk, reads and writes typically complete in well under a millisecond.

> 💡 **Analogy:** If a relational database is a filing cabinet, Redis is a sticky note board — nothing beats it for grabbing or updating a single piece of information instantly, but it's not built for complex, structured record-keeping.

---

## 1.2 Installing And redis-cli

Redis runs as a server process listening on port `6379` by default. **redis-cli** is the official command-line client:

```bash
redis-cli
127.0.0.1:6379> PING
PONG
```

---

## 1.3 Redis As An In-Memory Store

Because Redis keeps its dataset in memory, it's dramatically faster than a disk-based database for simple lookups — but it also means the amount of data Redis can hold is limited by available RAM, unlike disk-backed databases that can scale into terabytes cheaply. This trade-off is exactly why Redis is usually used alongside a primary database rather than replacing one.

---

## 1.4 Keys And Basic Commands

```bash
SET user:1:name "Ana"
GET user:1:name
EXISTS user:1:name
DEL user:1:name
KEYS user:*          # use with caution on large datasets
```

- Keys are just strings, and a common convention is to namespace them with colons, like `user:1:name`.
- `KEYS` scans the *entire* keyspace and can be slow on large datasets — `SCAN` is the safer alternative in production.

[Previous](./[0]-Introduction-to-Redis.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[2]-Core-Redis-Data-Types.md)