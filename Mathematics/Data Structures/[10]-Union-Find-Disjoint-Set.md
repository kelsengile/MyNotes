[Previous](./[9]-Graphs.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[11]-Tries.md)

# Lesson 10 - Union-Find (Disjoint Set)

Some problems don't need the full machinery of a graph — they just need to answer one narrow, recurring question: "are these two things connected in the same group?" A union-find structure answers that question, and updates group membership, both in almost constant time, which makes it far leaner than re-running a graph traversal every time the question comes up.

## 10.1 The Disjoint Set ADT

A **disjoint set** (also called union-find) manages a collection of elements partitioned into non-overlapping ("disjoint") groups. It supports exactly two core operations:

- **find(x)** — returns an identifier for the group x currently belongs to.
- **union(x, y)** — merges the groups containing x and y into a single group.

A common way to check whether two elements are already connected is simply `find(x) == find(y)`.

The standard implementation represents each group as a tree, where every element points to a **parent**, and the group's identifier is whichever element sits at the root (pointing to itself):

```python
class UnionFind:
    def __init__(self, elements):
        self.parent = {e: e for e in elements}   # everyone starts as their own group

    def find(self, x):
        while self.parent[x] != x:
            x = self.parent[x]
        return x

    def union(self, x, y):
        root_x, root_y = self.find(x), self.find(y)
        if root_x != root_y:
            self.parent[root_x] = root_y   # attach one tree under the other
```

```python
uf = UnionFind(["A", "B", "C", "D"])
uf.union("A", "B")
uf.union("C", "D")
print(uf.find("A") == uf.find("B"))   # True — same group
print(uf.find("A") == uf.find("C"))   # False — different groups
uf.union("B", "C")
print(uf.find("A") == uf.find("D"))   # True — now merged into one group
```

## 10.2 Union by Rank/Size and Path Compression

The naive implementation above has a weakness: repeatedly unioning trees in the wrong order can produce a long, thin chain, making `find` degrade toward O(n) — the same failure mode an unbalanced BST has (Lesson 7). Two optimizations fix this:

**Union by rank/size**: when merging two groups, always attach the *smaller* (or shorter) tree under the root of the *larger* one, rather than picking arbitrarily. This keeps the resulting trees shallow.

```python
def union(self, x, y):
    root_x, root_y = self.find(x), self.find(y)
    if root_x == root_y:
        return
    if self.size[root_x] < self.size[root_y]:
        root_x, root_y = root_y, root_x
    self.parent[root_y] = root_x
    self.size[root_x] += self.size[root_y]
```

**Path compression**: every time `find(x)` walks up to the root, re-point every node it passed through directly to that root. This means the *next* `find` on any of those nodes is instant, flattening the tree over time.

```python
def find(self, x):
    if self.parent[x] != x:
        self.parent[x] = self.find(self.parent[x])   # point straight to the root
    return self.parent[x]
```

Combining both optimizations gives an amortized time complexity of nearly O(1) per operation — technically O(α(n)), where α is the *inverse Ackermann function*, a value that grows so slowly it's smaller than 5 for any input size that could ever exist in practice. In practical terms: with both optimizations, union-find operations are effectively constant time.

## 10.3 Use Cases (Cycle Detection, Kruskal's Algorithm)

**Cycle detection in an undirected graph**: process each edge (u, v) one at a time. Before adding it, check `find(u) == find(v)` — if they're already in the same group, this edge would create a cycle, since u and v are already connected by some other path. Otherwise, add the edge and `union(u, v)`.

```python
def has_cycle(edges, vertices):
    uf = UnionFind(vertices)
    for u, v in edges:
        if uf.find(u) == uf.find(v):
            return True   # u and v already connected — this edge closes a cycle
        uf.union(u, v)
    return False
```

**Kruskal's algorithm** (for finding a graph's minimum spanning tree — the cheapest set of edges that connects every vertex) is a direct extension of the same idea: sort all edges by weight, then walk through them from cheapest to most expensive, using union-find to skip any edge that would create a cycle and keeping every edge that connects two previously separate groups.

**Other common use cases**: detecting connected components in a graph or image (e.g. flood-fill style grouping in image processing), network connectivity checks, and grouping accounts or entities that share some identifying trait (a common technique in fraud detection and deduplication systems).

[Previous](./[9]-Graphs.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[11]-Tries.md)
