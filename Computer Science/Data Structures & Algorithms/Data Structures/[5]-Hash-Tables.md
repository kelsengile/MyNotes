[Previous](./[4]-Queues.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[6]-Sets.md)

# Lesson 5 - Hash Tables

Every structure so far has one thing in common: finding an arbitrary value means either walking through elements one by one (O(n)) or relying on a sorted order (O(log n) with binary search). A hash table breaks that pattern — it gives average O(1) lookup, insertion, and deletion by keys, which is why it backs dictionaries, maps, and objects in nearly every language.

## 5.1 Hashing and Hash Functions

A hash table stores **key-value pairs**. Internally, it keeps a fixed-size array of "buckets." To decide where a given key belongs, it runs the key through a **hash function** — a function that converts a key (a string, number, object, whatever) into an integer, then maps that integer to a bucket index, typically with `hash(key) % number_of_buckets`.

```python
capacity = 8
def bucket_index(key, capacity):
    return hash(key) % capacity

bucket_index("apple", capacity)   # some integer 0–7, deterministic every time
```

A good hash function has two essential properties:

- **Deterministic**: the same key always produces the same hash, every time, so you can find something you previously stored.
- **Uniform distribution**: different keys should be spread evenly across buckets. A hash function that clusters most keys into a handful of buckets defeats the purpose — it degrades performance back toward O(n).

Because lookups, insertions, and deletions all reduce to "compute the hash, then check that one bucket," they're all O(1) on average.

```python
table = {}
table["apple"] = 1.20      # insert — O(1) average
table["banana"] = 0.65
print(table["apple"])       # lookup — O(1) average
del table["banana"]         # delete — O(1) average
```

## 5.2 Collision Resolution (Chaining, Open Addressing)

Since the number of possible keys vastly outnumbers the number of buckets, two different keys will sometimes hash to the same bucket index. This is a **collision**, and it's unavoidable — every hash table needs a strategy for handling it.

**Chaining** turns each bucket into its own small container — usually a linked list (or, in some modern implementations, a small tree) — that holds every key-value pair that hashed to that index. On a collision, the new pair is simply appended to that bucket's list.

```
Bucket 3: [("apple", 1.20)] → [("grape", 2.10)]   (both hashed to index 3)
```

Lookup then means hashing to the right bucket and doing a short linear scan through that bucket's list — still effectively O(1) as long as the buckets stay short on average.

**Open addressing** takes the opposite approach: every key-value pair lives directly in the array itself, one per slot. On a collision, the table **probes** for the next available slot according to some fixed sequence — the simplest being **linear probing**, which just checks the next index, then the next, and so on, wrapping around the array, until an empty slot is found.

```python
# Simplified linear probing insert
def insert(table, key, value):
    idx = hash(key) % len(table)
    while table[idx] is not None:
        idx = (idx + 1) % len(table)   # probe forward on collision
    table[idx] = (key, value)
```

Open addressing avoids the extra memory of per-bucket lists and tends to be more cache-friendly, but it's more sensitive to how full the table gets, and deletion is trickier (a naive removal can break the probe chain for later lookups, so slots are usually marked "deleted" rather than emptied outright).

## 5.3 Load Factor and Resizing

The **load factor** is the ratio of stored elements to total buckets:

```
load factor = number of elements / number of buckets
```

As the load factor climbs, collisions become more frequent and buckets get longer (chaining) or probe sequences get longer (open addressing) — both degrade performance toward O(n) in the worst case. To keep operations close to O(1), hash tables **resize** once the load factor crosses a threshold (commonly around 0.7): a new, larger backing array is allocated (typically double the size), and every existing key is **rehashed** into it, since the bucket index depends on the array's size.

This resize is an O(n) operation, but — exactly like the dynamic array from Lesson 1 — it happens rarely enough relative to individual insertions that the *amortized* cost per insertion stays O(1).

**Summary of average-case costs**:

| Operation | Average | Worst Case |
|---|---|---|
| Insert | O(1) | O(n) |
| Lookup | O(1) | O(n) |
| Delete | O(1) | O(n) |

The worst case (O(n)) happens only when the hash function performs badly and most keys collide into the same bucket — which is why choosing a good hash function matters as much as the resizing strategy itself.

[Previous](./[4]-Queues.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[6]-Sets.md)
