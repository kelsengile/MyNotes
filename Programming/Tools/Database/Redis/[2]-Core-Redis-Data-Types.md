[Previous](./[1]-Getting-Started-With-Redis.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[3]-Expiration-Persistence-And-Durability.md)

*Data Structures And Commands*

# Lesson 2 - Core Redis Data Types

## 2.1 Strings

The **String** is Redis's simplest and most-used type — it can hold text, numbers, or even binary data up to 512MB:

```bash
SET page:views 0
INCR page:views          # atomically increments to 1
INCRBY page:views 10
```

`INCR`/`INCRBY` being atomic makes Strings a natural fit for counters that many clients update simultaneously without race conditions.

---

## 2.2 Lists And Sets

A **List** is an ordered collection of strings, commonly used as a queue or stack:

```bash
RPUSH tasks "task1" "task2"    # push to the right (end)
LPOP tasks                     # pop from the left (front)
```

A **Set** is an unordered collection of unique strings:

```bash
SADD online_users "ana" "ben"
SISMEMBER online_users "ana"   # returns 1 (true)
SMEMBERS online_users
```

---

## 2.3 Hashes

A **Hash** stores field-value pairs under a single key — essentially a mini object, useful for representing something like a user record without a separate key for every attribute:

```bash
HSET user:1 name "Ana" email "ana@example.com" age 29
HGET user:1 name
HGETALL user:1
```

---

## 2.4 Sorted Sets

A **Sorted Set** is like a Set, but every member has an associated numeric **score**, keeping members automatically ordered — perfect for leaderboards and ranking:

```bash
ZADD leaderboard 100 "ana"
ZADD leaderboard 250 "ben"
ZRANGE leaderboard 0 -1 WITHSCORES     # lowest to highest score
ZREVRANGE leaderboard 0 2              # top 3 scores, highest first
```

[Previous](./[1]-Getting-Started-With-Redis.md) | [Table of Contents](./[0]-Introduction-to-Redis.md) | [Next](./[3]-Expiration-Persistence-And-Durability.md)