[Previous](./[5]-Hash-Tables.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[7]-Trees.md)

# Lesson 6 - Sets

A set is what you get when you take a hash table and throw away the values, keeping only the keys. Where earlier structures were built to hold ordered or positional data, a set is built around a single question: "have I seen this before?" — and answering it as fast as possible.

## 6.1 The Set ADT (Uniqueness, No Order Guarantee)

A set is an **abstract data type (ADT)** — a description of *behavior*, not a specific implementation — defined by two rules:

1. **Uniqueness**: a set never contains duplicate elements. Adding an element that's already present has no effect.
2. **No guaranteed order**: unlike an array or list, a set makes no promise about the order elements come back in when you iterate over it.

The core operations are:

- **add(value)** — insert an element (no-op if it's already there).
- **remove(value)** — delete an element.
- **contains(value)** — check membership.

```python
seen = set()
seen.add("apple")
seen.add("banana")
seen.add("apple")     # no effect — already present
print("apple" in seen) # True — contains check
print(len(seen))        # 2, not 3
```

This is exactly the property that makes sets useful for deduplication: dropping any collection into a set and reading it back out automatically removes duplicates.

```python
names = ["sam", "ana", "sam", "leo", "ana"]
unique_names = set(names)   # {"sam", "ana", "leo"}
```

## 6.2 Hash-Based Sets vs. Tree-Based Sets

**Hash-based sets** (the default `set` in Python, `HashSet` in Java) are implemented directly on top of a hash table (Lesson 5), just discarding the value half of each pair. This gives average O(1) add, remove, and contains — the fastest possible membership checking — at the cost of no ordering guarantee whatsoever; iterating twice over the same set isn't even guaranteed to return elements in the same order.

**Tree-based sets** (`TreeSet` in Java, `std::set` in C++) are implemented on top of a self-balancing binary search tree (previewed in Lesson 7). This costs more per operation — O(log n) for add, remove, and contains — but in exchange, elements are always kept in sorted order, so iterating produces them from smallest to largest, and range queries ("give me everything between 10 and 50") become efficient.

| | Hash-Based Set | Tree-Based Set |
|---|---|---|
| add / remove / contains | O(1) average | O(log n) |
| Iteration order | Unspecified | Sorted |
| Range queries | Not efficient | Efficient |

Choose a hash-based set by default when all you need is fast membership testing. Reach for a tree-based set specifically when you need the elements sorted, or need to efficiently query ranges of values.

## 6.3 Common Set Operations (Union, Intersection, Difference)

Because sets model mathematical sets, they support the same operations from set theory:

- **Union** (`A | B`): every element in *either* set.
- **Intersection** (`A & B`): only elements present in *both* sets.
- **Difference** (`A - B`): elements in `A` that are *not* in `B`.
- **Symmetric difference** (`A ^ B`): elements in exactly one of the two sets, not both.

```python
students_taking_math = {"Alice", "Bob", "Carol"}
students_taking_cs = {"Bob", "Carol", "Dan"}

students_taking_math | students_taking_cs   # {"Alice","Bob","Carol","Dan"} — union
students_taking_math & students_taking_cs   # {"Bob","Carol"}               — intersection
students_taking_math - students_taking_cs   # {"Alice"}                     — difference
students_taking_math ^ students_taking_cs   # {"Alice","Dan"}               — symmetric difference
```

For a hash-based set, each of these operations runs in roughly O(min(len(A), len(B))) to O(len(A) + len(B)) — far faster than the nested loops you'd need to compute the same results by comparing every element of one list against every element of another (which would be O(n × m)).

**Common use cases**: removing duplicates from a collection, fast membership testing (e.g. "has this user already voted?", "is this word in the dictionary?"), tracking visited nodes during graph traversal (Lesson 9), and comparing groups of data (finding common tags between two articles, finding permissions a user is missing, etc.).

[Previous](./[5]-Hash-Tables.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[7]-Trees.md)
