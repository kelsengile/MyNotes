[Previous](./[3]-Time-Complexity-Analysis.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[5]-Recurrence-Relations.md)

# Lesson 4 - Space Complexity

## 4.1 Auxiliary Space vs. Total Space

**Space complexity** measures how much memory an algorithm needs as a function of input size `n`, using the same Big-O notation used for time. It's analyzed with the same instincts as time complexity — count how memory usage grows, drop constants and lower-order terms — but the thing being counted is memory, not operations.

There's an important distinction between two kinds of space:

- **Total space** — all the memory the algorithm uses, including the space taken up by the input itself.
- **Auxiliary space** — only the *extra* memory the algorithm uses beyond the input, such as temporary variables, helper data structures, or recursive call stacks.

Auxiliary space is usually the more meaningful number, since it isolates what the algorithm itself costs rather than counting memory the caller already committed to by providing the input in the first place. When people say an algorithm "sorts in-place" or uses "O(1) extra space," they're talking about auxiliary space — the input array still takes up O(n) memory, but the algorithm doesn't need to allocate anything beyond that.

```python
def sum_array(arr):
    total = 0            # O(1) auxiliary space — one variable, regardless of len(arr)
    for x in arr:
        total += x
    return total
# Auxiliary space: O(1).  Total space: O(n), dominated by the input array itself.

def double_array(arr):
    result = []           # a new array, growing with the input
    for x in arr:
        result.append(x * 2)
    return result
# Auxiliary space: O(n) — a whole new array is allocated, proportional to input size.
```

Recursive functions have a space cost that's easy to overlook: every recursive call adds a new frame to the **call stack**, and each frame occupies memory until that call returns. A recursive function that goes `n` calls deep uses O(n) auxiliary space for the call stack alone, even if each individual call does no other memory allocation:

```python
def recursive_sum(arr, n):
    if n <= 0:
        return 0
    return arr[n - 1] + recursive_sum(arr, n - 1)
# Time: O(n).  Auxiliary space: O(n), from n stacked recursive calls.
```

This is a big part of why an iterative version of the same logic is sometimes preferred: it does the same O(n) time work with O(1) auxiliary space, since there's no growing call stack.

---

## 4.2 Space-Time Tradeoffs

Very often, an algorithm can be made faster in time by using more memory, or leaner in memory at the cost of more time — this exchange is called a **space-time tradeoff**, and recognizing when it's worth making is a core algorithm design skill.

**Memoization** (Lesson 6.2 in the Algorithms topic) is the clearest example: naive recursive Fibonacci uses O(1) auxiliary space (ignoring the call stack) but O(2ⁿ) time, because it recomputes the same values over and over. Adding a cache trades that away — now it uses O(n) space to store previously computed results, but drops the time cost all the way down to O(n), because no subproblem is ever solved twice.

```python
# No extra space (besides call stack) — but exponential time
def fib_naive(n):
    if n <= 1:
        return n
    return fib_naive(n - 1) + fib_naive(n - 2)

# O(n) extra space (the cache) — but linear time
def fib_memo(n, memo=None):
    if memo is None:
        memo = {}
    if n in memo:
        return memo[n]
    if n <= 1:
        return n
    memo[n] = fib_memo(n - 1, memo) + fib_memo(n - 2, memo)
    return memo[n]
```

Another classic example is **hash maps used for fast lookup**: storing every element in a hash set costs O(n) extra space, but turns membership checks that would otherwise take O(n) time (scanning a list) into O(1) time on average. This is exactly what made `has_duplicate_b` from Lesson 1.1 so much faster than `has_duplicate_a` — it spent O(n) space to buy back time.

**Precomputed lookup tables** follow the same idea at a larger scale: if a value needs to be computed repeatedly (e.g. factorials, prime checks, or distances), computing every needed value once ahead of time and storing it in an array trades upfront memory and setup time for near-instant lookups afterward.

There's no universally "correct" side of this tradeoff — it depends on what's scarce in a given situation. A mobile app running on a memory-constrained device might prefer the slower, leaner algorithm; a backend service answering millions of requests per second might happily spend gigabytes of RAM on caching to shave milliseconds off every response. Recognizing that time and space can often be traded for one another — and knowing which one your situation can afford to spend — is as important as knowing the raw complexity numbers themselves.

[Previous](./[3]-Time-Complexity-Analysis.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[5]-Recurrence-Relations.md)
