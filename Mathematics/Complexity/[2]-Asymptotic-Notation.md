[Previous](./[1]-Why-We-Analyze-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[3]-Time-Complexity-Analysis.md)

# Lesson 2 - Asymptotic Notation

## 2.1 Big-O Notation

**Big-O notation** describes an **upper bound** on how an algorithm's running time (or space use) grows as the input size `n` grows, ignoring constant factors and lower-order terms. It answers the question: "in the worst case, how badly can this scale?"

Formally, `f(n) = O(g(n))` means there exist positive constants `c` and `n₀` such that `f(n) ≤ c · g(n)` for all `n ≥ n₀`. Informally: once `n` is large enough, `g(n)` (times some constant) is always at least as big as `f(n)`.

The reason constants and lower-order terms get dropped is that they stop mattering as `n` grows large. An algorithm that does `3n² + 100n + 500` operations is still `O(n²)`, because as `n` grows toward infinity, the `n²` term completely dominates the other two — whether the coefficient is 3 or 300 doesn't change the *shape* of the growth curve, just where it starts.

```python
def sum_pairs(arr):
    total = 0
    for i in range(len(arr)):       # n iterations
        for j in range(len(arr)):   # n iterations, for each i
            total += arr[i] + arr[j]
    return total
# n * n = n² operations -> O(n²), regardless of small constant work per iteration
```

Big-O is specifically about the **worst case** by convention (though the same notation can technically describe any case — see 3.2 for how best/average/worst case fit in). When someone says "this algorithm is O(n log n)," they usually mean: no matter how bad the input gets, it will never do more than roughly `n log n` work.

---

## 2.2 Big-Omega and Big-Theta

Big-O only tells half the story — it's a ceiling, not an exact measurement. Two more notations round out the picture:

- **Big-Omega (Ω)** describes a **lower bound** — the algorithm will take *at least* this long, no matter how favorable the input. `f(n) = Ω(g(n))` means there exist constants `c` and `n₀` such that `f(n) ≥ c · g(n)` for all `n ≥ n₀`.
- **Big-Theta (Θ)** describes a **tight bound** — both an upper *and* lower bound, meaning the algorithm's growth rate is essentially pinned down exactly (up to constant factors). `f(n) = Θ(g(n))` means `f(n)` is both `O(g(n))` and `Ω(g(n))`.

A useful analogy: if Big-O is "at most this slow" and Big-Omega is "at least this slow," then Big-Theta is "exactly this fast, no more, no less" (again, ignoring constants).

For example, linear search (Lesson 2.1 in the Algorithms topic) is `O(n)` in the worst case (the target is last, or missing), but `Ω(1)` in the best case (the target is first). It is *not* `Θ(n)` overall, because its best and worst cases differ in growth rate — but it *is* `Θ(n)` if you're specifically discussing its worst-case behavior. Merge sort, by contrast, always does `Θ(n log n)` work regardless of input arrangement, so its running time is tightly bounded in every case.

In casual conversation and most technical interviews, people say "Big-O" even when they technically mean Big-Theta — describing the overall tight-ish growth rate of an algorithm's typical or worst-case behavior. It's useful to know the distinction, but don't be surprised if the terms get used loosely in practice.

---

## 2.3 Common Growth Rates (O(1) to O(n!))

From fastest to slowest, here are the growth rates you'll encounter constantly:

| Notation      | Name             | Example                                              |
|---------------|------------------|-------------------------------------------------------|
| O(1)          | Constant         | Accessing an array element by index                   |
| O(log n)      | Logarithmic      | Binary search                                          |
| O(n)          | Linear           | Linear search, a single pass through an array          |
| O(n log n)    | Linearithmic     | Merge sort, quick sort (average case), heap sort        |
| O(n²)         | Quadratic        | Bubble sort, comparing all pairs in a list              |
| O(n³)         | Cubic            | Naive matrix multiplication                             |
| O(2ⁿ)         | Exponential      | Naive recursive Fibonacci, generating all subsets       |
| O(n!)         | Factorial        | Generating all permutations, brute-force traveling salesman |

The practical difference between these grows enormous as `n` increases. For `n = 20`:

- O(log n) ≈ 4 operations
- O(n) = 20 operations
- O(n log n) ≈ 86 operations
- O(n²) = 400 operations
- O(2ⁿ) ≈ 1,048,576 operations
- O(n!) ≈ 2,432,902,008,176,640,000 operations

This is why recognizing which growth rate an algorithm falls into matters so much more than fine-tuning constants: an O(n log n) algorithm will beat an O(n²) algorithm on large inputs even if the O(n²) one has been hand-optimized to be "fast" in absolute terms, simply because the gap between the two curves widens without bound as `n` grows.

---

## 2.4 Little-o and Little-Omega Notation

Big-O and Big-Omega describe bounds that the function may or may not actually reach — Big-O allows `f(n)` to grow *exactly* as fast as `g(n)`, not just slower. **Little-o** and **little-omega** are the strict versions of these same ideas, describing bounds the function gets arbitrarily close to but never actually equals in growth rate.

- `f(n) = o(g(n))` ("little-o") means `f(n)` grows **strictly slower** than `g(n)` — for *every* positive constant `c`, `f(n) < c · g(n)` eventually holds. For example, `n = o(n²)`, but `n ≠ o(n)` (since `n` doesn't grow strictly slower than itself).
- `f(n) = ω(g(n))` ("little-omega") means `f(n)` grows **strictly faster** than `g(n)`, the mirror image of little-o.

In practice, little-o and little-omega show up far less often than Big-O, Big-Omega, and Big-Theta — most day-to-day algorithm analysis (and virtually all technical interviews) sticks to Big-O. But they're worth recognizing: if you ever see a claim like "this algorithm runs in `o(n²)` time," it's making the stronger claim that the algorithm is *asymptotically better* than quadratic, not merely bounded by it.

[Previous](./[1]-Why-We-Analyze-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Complexity.md) | [Next](./[3]-Time-Complexity-Analysis.md)
