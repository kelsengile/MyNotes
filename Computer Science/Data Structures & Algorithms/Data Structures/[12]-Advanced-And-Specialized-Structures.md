[Previous](./[11]-Tries.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md)

# Lesson 12 - Advanced & Specialized Structures

Every structure so far solves a general-purpose problem — ordered storage, key lookup, hierarchy, connectivity. This final lesson is a tour of five structures built to solve much narrower problems exceptionally well: fast range queries, probabilistic membership testing, and disk-efficient storage. You won't reach for these as often, but knowing they exist — and what specific problem each one solves — is what lets you recognize the situations where a general-purpose structure would be the wrong tool.

## 12.1 Segment Trees

A **segment tree** answers range queries — "what's the sum/min/max of elements from index i to j?" — in O(log n), while still allowing individual elements to be updated in O(log n). A plain array can do one of these fast but not both: computing a range sum directly is O(n), while updating a single element is O(1).

A segment tree is a binary tree built over the array, where each node covers a range of indices and stores the aggregate (sum, min, max — whatever's needed) of that range. Leaf nodes represent single elements; each internal node's value is computed from its two children.

```python
class SegmentTree:
    def __init__(self, data):
        n = len(data)
        self.n = n
        self.tree = [0] * (2 * n)
        for i in range(n):
            self.tree[n + i] = data[i]
        for i in range(n - 1, 0, -1):
            self.tree[i] = self.tree[2 * i] + self.tree[2 * i + 1]

    def update(self, i, value):
        i += self.n
        self.tree[i] = value
        while i > 1:
            i //= 2
            self.tree[i] = self.tree[2 * i] + self.tree[2 * i + 1]

    def range_sum(self, l, r):   # sum of elements in [l, r)
        l += self.n
        r += self.n
        total = 0
        while l < r:
            if l % 2 == 1:
                total += self.tree[l]
                l += 1
            if r % 2 == 1:
                r -= 1
                total += self.tree[r]
            l //= 2
            r //= 2
        return total
```

**Use cases**: range-sum/min/max queries with frequent updates — competitive programming, computational geometry, and interval-heavy analytics (e.g. "what's the minimum stock price between day 10 and day 40?" on data that keeps updating).

## 12.2 Fenwick Trees (Binary Indexed Trees)

A **Fenwick tree**, also called a **binary indexed tree (BIT)**, solves the same core problem as a segment tree — range sum queries with point updates, both in O(log n) — but with a much smaller, simpler implementation: just a single array, using bit manipulation to navigate implicit tree relationships instead of building explicit nodes.

```python
class FenwickTree:
    def __init__(self, size):
        self.tree = [0] * (size + 1)

    def update(self, i, delta):
        i += 1   # Fenwick trees are conventionally 1-indexed
        while i < len(self.tree):
            self.tree[i] += delta
            i += i & (-i)         # move to the next relevant index

    def prefix_sum(self, i):
        i += 1
        total = 0
        while i > 0:
            total += self.tree[i]
            i -= i & (-i)          # move to the parent range
        return total

    def range_sum(self, l, r):     # sum of elements in [l, r]
        return self.prefix_sum(r) - (self.prefix_sum(l - 1) if l > 0 else 0)
```

The trick is the `i & (-i)` operation, which isolates the lowest set bit of `i` — this is what lets each array slot implicitly represent a specific range without ever storing explicit tree pointers.

**Fenwick vs. segment tree**: a Fenwick tree is simpler, faster to code, and uses less memory, but it's naturally limited to prefix/range **sums** (and similar invertible operations). A segment tree is more general — it can support min, max, GCD, and other operations a Fenwick tree can't handle as cleanly, at the cost of a somewhat heavier implementation.

## 12.3 B-Trees (and Why Databases Use Them)

A **B-tree** is a self-balancing tree, similar in spirit to the red-black trees from Lesson 7, but generalized so each node can hold *multiple* keys and have *more than two* children (a parameter usually called the tree's **order** or **branching factor**).

```
         [ 10 | 20 ]
        /     |     \
   [1..9]  [11..19]  [21..30]
```

Where a binary tree node makes one decision (go left or right), a B-tree node with many keys lets you make one decision among many branches at once — which means a B-tree stays extremely **shallow** even with millions of entries, since each level fans out so widely.

That shallowness is precisely why B-trees are the standard structure behind database indexes and filesystems. Reading from disk is orders of magnitude slower than reading from memory, and the cost that matters most is the *number of disk reads*, not the number of comparisons. A B-tree node is sized to match a disk block, so each level of the tree corresponds to exactly one disk read — and because the tree is so shallow, looking up any record takes only a handful of disk reads even across a massive dataset, compared to the much deeper tree a binary structure would require.

**Use cases**: relational database indexes (MySQL's InnoDB, PostgreSQL), filesystems (NTFS, ext4, HFS+), and any system where data lives on disk and minimizing the number of I/O operations matters more than minimizing in-memory comparisons.

## 12.4 Skip Lists

A **skip list** is a linked list (Lesson 2) enhanced with multiple "levels" of shortcuts, giving it BST-like O(log n) search, insert, and delete — without any rotations or rebalancing logic.

The bottom level is an ordinary sorted linked list containing every element. Each level above it contains a randomly chosen *subset* of the elements below it, acting as an express lane that lets a search skip over large chunks of the list at once.

```
Level 2:  1 --------------------> 9
Level 1:  1 -------> 5 --------> 9
Level 0:  1 -> 3 -> 5 -> 7 -> 9
```

A search starts at the top level and moves right until the next node would overshoot the target, then drops down a level and continues — narrowing in on the target the same way binary search narrows in on an array index, just using express-lane pointers instead of index arithmetic.

```python
import random

class SkipListNode:
    def __init__(self, value, level):
        self.value = value
        self.forward = [None] * (level + 1)

class SkipList:
    def __init__(self, max_level=16, p=0.5):
        self.max_level = max_level
        self.p = p
        self.head = SkipListNode(None, max_level)
        self.level = 0

    def _random_level(self):
        lvl = 0
        while random.random() < self.p and lvl < self.max_level:
            lvl += 1
        return lvl
```

Because each node's level is chosen randomly rather than through deliberate rebalancing, skip lists are simpler to implement correctly than AVL or red-black trees while achieving the same expected O(log n) performance. This simplicity is why skip lists are used in Redis's sorted sets and in some database engines' in-memory indexes.

## 12.5 Bloom Filters

A **Bloom filter** trades certainty for extreme space efficiency. It answers a single question — "have I possibly seen this element before?" — using a fixed-size bit array and several hash functions, with no regard for how many elements have been added.

```python
class BloomFilter:
    def __init__(self, size, num_hashes):
        self.size = size
        self.num_hashes = num_hashes
        self.bits = [0] * size

    def _hashes(self, item):
        for seed in range(self.num_hashes):
            yield hash((item, seed)) % self.size

    def add(self, item):
        for idx in self._hashes(item):
            self.bits[idx] = 1

    def might_contain(self, item):
        return all(self.bits[idx] == 1 for idx in self._hashes(item))
```

Adding an element sets several bits (one per hash function) to 1. Checking membership hashes the item the same way and checks whether *all* those bit positions are set. This gives Bloom filters two defining properties:

- **No false negatives**: if `might_contain` says "no," the element was definitely never added.
- **Possible false positives**: if `might_contain` says "yes," the element was *probably* added — but different elements can hash to the same bit positions, so it's not a guarantee. The false-positive rate rises as more elements are added and the bit array fills up, and can be tuned by adjusting the array's size and the number of hash functions used.

This is the fundamental trade-off compared to a hash set (Lesson 6): a Bloom filter uses vastly less memory (a handful of bits per element instead of storing the full element), at the cost of occasional false positives and no ability to retrieve or remove elements.

**Use cases**: quick pre-checks before an expensive operation — e.g. checking "might this URL be malicious?" before a full database lookup, checking whether a username is *possibly* taken before hitting the database, deduplication in large-scale data pipelines, and caching layers (like web browsers and CDNs) that need to cheaply rule out "definitely not seen" before falling back to a slower, authoritative check.

---

That closes out the Data Structures topic. With arrays, linked lists, stacks, queues, hash tables, sets, trees, heaps, graphs, union-find, tries, and this lesson's specialized structures, you now have the full toolkit referenced throughout the DSA Fundamentals and Complexity topics — the next step is applying them directly in the Algorithms topic.

[Previous](./[11]-Tries.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md)
