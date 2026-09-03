[Previous](./[4]-Space-Complexity.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[6]-Amortized-Analysis.md)

# Lesson 5 - Recurrence Relations

## 5.1 Writing a Recurrence Relation

A **recurrence relation** is an equation that defines a recursive algorithm's running time, `T(n)`, in terms of the running time on smaller inputs, plus whatever extra work happens at the current level. Writing one down is the standard first step in analyzing any recursive algorithm rigorously.

The general recipe for writing one:

1. Identify how many recursive calls are made, and how large each subproblem is.
2. Identify how much non-recursive work happens at each call (splitting the input, combining results, etc.).
3. Identify the base case — the input size small enough that the function returns directly without recursing.

Take merge sort (Lesson 1.2 in the Algorithms topic): it makes **2** recursive calls, each on a subproblem of size **n/2**, plus **O(n)** work to merge the two sorted halves back together. That gives:

```
T(n) = 2T(n/2) + O(n)
T(1) = O(1)   (base case)
```

Binary search (Lesson 2.2 in the Algorithms topic) makes **1** recursive call, on a subproblem of size **n/2**, plus **O(1)** work to compare against the middle element:

```
T(n) = T(n/2) + O(1)
T(1) = O(1)
```

Naive recursive Fibonacci (Lesson 6.1 in the Algorithms topic) makes **2** recursive calls, each on a subproblem of size **n-1** and **n-2** respectively, plus O(1) work to add the results:

```
T(n) = T(n-1) + T(n-2) + O(1)
T(0) = T(1) = O(1)
```

Once a recurrence is written down, the goal is to **solve** it — to convert this recursive definition into a closed-form Big-O expression. The next two sections cover two different tools for doing that.

---

## 5.2 Solving with the Recursion Tree Method

The **recursion tree method** solves a recurrence by literally drawing out the tree of recursive calls, level by level, figuring out how much work happens at each level, and summing across all levels.

Working through merge sort's recurrence, `T(n) = 2T(n/2) + O(n)`:

```
Level 0:            n                       -> work: n
                    /  \
Level 1:         n/2    n/2                 -> work: n/2 + n/2 = n
                 /  \    /  \
Level 2:      n/4  n/4 n/4  n/4              -> work: 4 * (n/4) = n
                ...
Level log n:    1  1  1  1  1  1  1  1  ...  -> work: n * 1 = n
```

At every level, the sizes of the subproblems shrink (each is half the size of the level above), but the *number* of subproblems doubles — so the total work done at each level stays constant at roughly `n`. The number of levels is `log₂ n`, since the subproblem size is cut in half at each level until it reaches 1. Total work: `n` per level × `log n` levels = **O(n log n)** — matching the well-known complexity of merge sort.

The same method applied to naive Fibonacci's recurrence, `T(n) = T(n-1) + T(n-2) + O(1)`, produces a tree that isn't as clean (it's not perfectly balanced, since one branch shrinks by 1 and the other by 2), but the key observation still holds: the tree has two children at every node, and its depth is roughly `n`, so the total number of nodes — and therefore the total work — grows as **O(2ⁿ)**.

The recursion tree method is especially useful because it builds real intuition for *why* a recurrence resolves the way it does, rather than just mechanically producing an answer — which makes it a great tool for double-checking a bound produced by the Master Theorem below, or for tackling a recurrence that doesn't fit the Master Theorem's required shape at all.

---

## 5.3 The Master Theorem

The **Master Theorem** is a shortcut that solves recurrences of a specific common form directly, without needing to draw a recursion tree by hand every time. It applies to recurrences shaped like:

```
T(n) = a·T(n/b) + f(n)
```

where:
- `a` = the number of recursive calls (subproblems) made at each step
- `b` = the factor by which the input size shrinks in each subproblem
- `f(n)` = the cost of the non-recursive work at each step (dividing and combining)

The theorem compares `f(n)` against `n^(log_b a)` — the amount of work implied purely by the branching structure of the recursion — and falls into one of three cases:

1. **If `f(n)` grows slower than `n^(log_b a)`** → the recursive calls dominate, and `T(n) = O(n^(log_b a))`.
2. **If `f(n)` grows at the same rate as `n^(log_b a)`** → the work is spread evenly across all `log n` levels, and `T(n) = O(n^(log_b a) · log n)`.
3. **If `f(n)` grows faster than `n^(log_b a)`** (and satisfies a technical "regularity condition") → the work at the top level dominates, and `T(n) = O(f(n))`.

Applying it to merge sort's recurrence, `T(n) = 2T(n/2) + O(n)`: here `a = 2`, `b = 2`, and `f(n) = n`. Computing `n^(log_b a) = n^(log₂ 2) = n^1 = n`. Since `f(n) = n` grows at the *same rate* as `n^(log_b a) = n`, this is **Case 2**, giving `T(n) = O(n log n)` — matching the recursion tree result exactly.

Applying it to binary search's recurrence, `T(n) = T(n/2) + O(1)`: here `a = 1`, `b = 2`, and `f(n) = 1` (constant). Computing `n^(log_b a) = n^(log₂ 1) = n^0 = 1`. Since `f(n) = 1` grows at the same rate as `n^(log_b a) = 1`, this is again **Case 2**, giving `T(n) = O(1 · log n) = O(log n)` — matching binary search's well-known complexity.

The Master Theorem is fast and reliable, but it only applies to recurrences that fit its exact form (a fixed number of equally-sized subproblems). Recurrences with unevenly-sized subproblems, like naive Fibonacci's `T(n) = T(n-1) + T(n-2) + O(1)`, don't fit the required shape at all — those need the recursion tree method, the substitution method (Lesson 3.5), or another dedicated technique instead.

[Previous](./[4]-Space-Complexity.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[6]-Amortized-Analysis.md)
