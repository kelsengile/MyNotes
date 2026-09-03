[Previous](./[5]-Recurrence-Relations.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[7]-Complexity-Classes.md)

# Lesson 6 - Amortized Analysis

## 6.1 What Is Amortized Complexity?

So far, complexity analysis has focused on a **single operation's** worst case. **Amortized analysis** asks a different question: over a long **sequence** of operations, what's the *average* cost per operation — even if some individual operations are occasionally much more expensive than others?

The classic example is a **dynamic array** (like Python's `list`, or a `Vector`/`ArrayList` in other languages), which grows by doubling its underlying capacity whenever it runs out of room.

```python
class DynamicArray:
    def __init__(self):
        self.capacity = 1
        self.size = 0
        self.data = [None] * self.capacity

    def append(self, value):
        if self.size == self.capacity:
            self.capacity *= 2                    # double the capacity
            new_data = [None] * self.capacity
            for i in range(self.size):
                new_data[i] = self.data[i]          # copy every existing element over
            self.data = new_data

        self.data[self.size] = value
        self.size += 1
```

Most calls to `append` are O(1) — there's room, so the value just gets placed at the end. But every so often, when the array is full, a single call to `append` has to allocate a new array and copy **every** existing element over — an O(n) operation. Taken in isolation, that single call's *worst-case* complexity is O(n), which would suggest that appending to a dynamic array is expensive.

But that's misleading, because those expensive O(n) resizes happen rarely, and become *rarer* as the array grows (doubling means resizes happen at sizes 1, 2, 4, 8, 16... — exponentially further apart). Amortized analysis captures this by spreading the cost of each expensive resize across all the cheap operations that came before it, arriving at the **amortized cost per operation** — the true average cost per operation over a long sequence, which for dynamic array appends comes out to **O(1)**, even though occasional individual calls cost O(n).

It's worth being precise about what amortized analysis is *not*: it is not the same as average-case analysis (Lesson 3.2). Average case assumes something about the *distribution of inputs* (e.g. "the target is equally likely to be anywhere"). Amortized analysis makes no assumption about randomness at all — it's a guarantee that holds for *every* possible sequence of operations, because it's based on the deterministic structure of how costs accumulate over time, not on luck.

---

## 6.2 Aggregate, Accounting, and Potential Methods

There are three standard techniques for formally proving an amortized bound, each offering a different way of thinking about the same underlying idea.

**The Aggregate Method** simply computes the total cost of a sequence of `n` operations, then divides by `n` to get the amortized cost per operation. For the dynamic array: over `n` appends, resizes happen when the size passes 1, 2, 4, 8, ..., up to `n`, each resize costing an amount proportional to its size. Summing that geometric series (`1 + 2 + 4 + ... + n ≈ 2n`) gives a total resizing cost of O(n) across all `n` appends — combined with the O(n) cost of the cheap appends themselves, the total work for `n` appends is O(n), so dividing by `n` operations gives an amortized cost of **O(1) per append**.

**The Accounting Method** assigns each operation an amortized "charge" — a fixed price you pretend every operation costs, whether or not that's its true cost in that instance. Cheap operations get overcharged, and the surplus is saved up as "credit"; expensive operations then get paid for out of that saved credit instead of their true cost. For the dynamic array: charge every `append` a flat rate of (say) 3 units. The 1 unit actually needed to place the new element is spent immediately, and the other 2 units are banked as credit tied to that element. By the time the array needs to resize, there's enough banked credit sitting on the elements added since the last resize to pay for copying all of them — as long as the credit never goes negative, the flat charge is a valid amortized bound.

**The Potential Method** is a more formal generalization of the accounting method, using a "potential function" `Φ` that measures the amount of saved-up "energy" in the data structure's current state (analogous to the banked credit above, but expressed mathematically). The amortized cost of an operation is defined as its actual cost plus the *change* in potential it causes: `amortized cost = actual cost + Φ(after) - Φ(before)`. For the dynamic array, a natural potential function is `Φ = 2·size - capacity` (roughly, twice the number of elements minus the array's current capacity) — cheap appends increase this potential (banking energy), and a resize consumes a large chunk of it (spending the banked energy to pay for the copy), which is exactly the same intuition as the accounting method, expressed with a formula instead of an analogy.

All three methods are just different lenses on the same fact — occasional expensive operations are "paid for" by the many cheap operations around them — and different problems tend to be easier to reason about with one method over another. The aggregate method is usually the simplest to reach for first; the accounting and potential methods become more useful for structures with multiple different operation types (like a stack with both `push` and a multi-pop `pop_all`), where a single overall total is harder to compute directly.

Other classic examples of amortized analysis besides dynamic arrays include: incrementing a binary counter (most increments flip only the last bit, but occasionally a long carry chain flips many bits), and certain balanced tree or hash table operations where occasional rebalancing/rehashing is amortized against many cheap insertions.

[Previous](./[5]-Recurrence-Relations.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[7]-Complexity-Classes.md)
