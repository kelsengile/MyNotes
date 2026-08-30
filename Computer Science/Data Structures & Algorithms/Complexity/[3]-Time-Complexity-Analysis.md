[Previous](./[2]-Asymptotic-Notation.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[4]-Space-Complexity.md)

# Lesson 3 - Time Complexity Analysis

## 3.1 Counting Operations

The core skill behind time complexity analysis is simple in principle: count how many basic operations (comparisons, arithmetic, assignments) an algorithm performs, as a function of the input size `n`, then simplify that count down to its dominant term using Big-O.

```python
def example(arr):
    total = 0            # 1 operation
    for x in arr:         # runs n times
        total += x         # 1 operation, each time
    return total          # 1 operation
```

Counting exactly: 1 (initialize) + n (loop body) + 1 (return) = `n + 2` operations. In Big-O terms, the constant `+2` is dropped since it doesn't affect the growth rate as `n` gets large, leaving **O(n)**.

You don't need to count every single operation with this level of precision in practice — the goal is to identify the **dominant operation**: the one whose count grows fastest as `n` increases, since that's the term that survives simplification. A few shortcuts that make this fast:

- A loop that runs `n` times contributes a factor of `n`.
- Two independent (sequential, not nested) loops that each run `n` times add up: `O(n) + O(n) = O(n)` — Big-O keeps only the largest term, and doubling a linear count is still linear.
- Nested loops **multiply**: a loop inside a loop, both running `n` times, gives `O(n²)`.
- Constant-time operations (arithmetic, comparisons, array indexing, most dictionary/set operations) don't affect the growth rate and can usually be counted as O(1) each.

---

## 3.2 Best, Average, and Worst Case

The same algorithm can behave differently depending on *which* input of size `n` it receives, not just how large `n` is. Take linear search:

```python
def linear_search(arr, target):
    for i, value in enumerate(arr):
        if value == target:
            return i
    return -1
```

- **Best case** — the target is the very first element. Just 1 comparison, regardless of `n`. This is **O(1)**.
- **Worst case** — the target is the last element, or missing entirely. `n` comparisons. This is **O(n)**.
- **Average case** — assuming the target is equally likely to be anywhere (or absent), the expected number of comparisons is roughly `n/2`, which is still **O(n)** once constants are dropped.

By convention, when an algorithm's complexity is stated without qualification (like "linear search is O(n)"), it almost always refers to the **worst case** — this is the most useful guarantee, since it tells you the absolute ceiling on how bad things can get, regardless of how the input happens to be arranged. Average case is also commonly discussed (e.g. "quick sort is O(n log n) on average"), especially when an algorithm's worst case is rare in practice but still theoretically possible. Best case is mentioned far less often, since a guarantee about the most favorable possible input isn't very reassuring.

---

## 3.3 Analyzing Loops and Nested Loops

**Single loops** that run a fixed number of times proportional to `n` contribute `O(n)`:

```python
for i in range(n):
    do_something()   # O(1) per call -> O(n) total
```

**Sequential loops** (one after another, not nested) add together, and Big-O keeps only the largest term:

```python
for i in range(n):
    do_something()      # O(n)
for j in range(n):
    do_something_else() # O(n)
# O(n) + O(n) = O(n)
```

**Nested loops** multiply. A loop inside a loop, both proportional to `n`, gives `O(n²)`:

```python
for i in range(n):
    for j in range(n):
        do_something()  # O(n * n) = O(n²)
```

Nested loops don't have to run over the same range to multiply — a loop of size `n` nested inside a loop of size `m` gives `O(n · m)`, not `O(n²)`, unless `n` and `m` happen to be equal.

**Loops with changing bounds** need a little more care. A common pattern:

```python
for i in range(n):
    for j in range(i, n):
        do_something()
```

The inner loop doesn't always run `n` times — it runs `n`, then `n-1`, then `n-2`, and so on down to 1. Summing that up: `n + (n-1) + (n-2) + ... + 1 = n(n+1)/2`, which is still **O(n²)** once the lower-order term and constant factor are dropped, even though the exact operation count is roughly half of a full `n × n` nested loop.

---

## 3.4 Analyzing Recursive Functions

Recursive functions are analyzed by counting **how many times the function calls itself** and **how much work happens outside the recursive calls** at each step. The starting point is usually to write a **recurrence relation** — an equation defining the running time `T(n)` in terms of the running time of smaller inputs (covered in depth in Lesson 5).

A simple recursive sum:

```python
def recursive_sum(arr, n):
    if n <= 0:
        return 0
    return arr[n - 1] + recursive_sum(arr, n - 1)
```

Each call does O(1) work (one addition) and makes exactly one recursive call on an input one smaller. That gives the recurrence `T(n) = T(n-1) + O(1)`, which unrolls to `T(n) = O(n)` — the same as an iterative loop, just expressed through the call stack instead.

Compare this to naive recursive Fibonacci (see Lesson 6.1 in the Algorithms topic):

```python
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

Here, each call does O(1) work but makes **two** recursive calls, each on an input roughly one smaller. That gives `T(n) = T(n-1) + T(n-2) + O(1)`, a branching recursion whose call count grows exponentially — **O(2ⁿ)**. The key difference from the recursive sum above: one recursive call per step accumulates linearly, but two recursive calls per step causes the number of calls to double at each level, which is what produces exponential growth.

---

## 3.5 The Substitution Method

The **substitution method** is a way to *prove* a guessed Big-O bound for a recurrence relation is correct, by substituting the guessed bound back into the recurrence and checking that the math holds up.

The process:

1. **Guess** a bound, based on intuition or the recursion's shape (e.g. "I think `T(n) = T(n-1) + O(1)` is `O(n)`").
2. **Assume** the guess holds for all smaller inputs: assume `T(k) ≤ c · k` for some constant `c`, for all `k < n`.
3. **Substitute** that assumption into the recurrence and show it implies the same bound holds for `n` too.

Working through `T(n) = T(n-1) + 1`, guessing `T(n) = O(n)`:

- Assume `T(n-1) ≤ c(n-1)` for some constant `c`.
- Substitute into the recurrence: `T(n) = T(n-1) + 1 ≤ c(n-1) + 1 = cn - c + 1`.
- This is `≤ cn` as long as `-c + 1 ≤ 0`, i.e. `c ≥ 1`.
- So for any `c ≥ 1`, the bound `T(n) ≤ cn` holds — confirming `T(n) = O(n)`.

The substitution method is more of a rigorous verification tool than a discovery tool — it works best when you already have a reasonable guess (often from the recursion tree method, covered in Lesson 5.2, or from simply recognizing a familiar pattern) and want to confirm it holds exactly. It's especially useful for recurrences that don't fit neatly into the Master Theorem's standard form (Lesson 5.3), where a direct proof is the only way to pin down the exact bound.

[Previous](./[2]-Asymptotic-Notation.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[4]-Space-Complexity.md)
