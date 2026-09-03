[Previous](./[5]-Greedy-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[7]-Backtracking.md)

# Lesson 6 - Dynamic Programming

## 6.1 Overlapping Subproblems & Optimal Substructure

Dynamic programming (DP) is a technique for solving problems by breaking them into subproblems, just like divide-and-conquer — but it applies specifically when those subproblems **overlap**, meaning the same subproblem gets solved multiple times if approached naively. DP solves each unique subproblem only once and reuses the result, instead of recomputing it.

A problem is a good candidate for DP when it has two properties:

- **Overlapping subproblems** — a naive recursive solution solves the exact same subproblem repeatedly. (This is the key difference from divide-and-conquer, Lesson 4, where subproblems like the two halves of an array in merge sort never overlap.)
- **Optimal substructure** — the optimal solution to the problem can be built from optimal solutions to its subproblems.

The classic illustration is computing Fibonacci numbers. A naive recursive solution:

```python
def fib_naive(n):
    if n <= 1:
        return n
    return fib_naive(n - 1) + fib_naive(n - 2)
```

This looks fine, but `fib_naive(5)` calls `fib_naive(3)` twice, `fib_naive(2)` three times, and so on — the number of redundant calls grows exponentially, giving a horrifying **O(2ⁿ)** running time. DP fixes this by remembering answers we've already computed.

---

## 6.2 Memoization vs. Tabulation

There are two ways to implement dynamic programming, and they produce identical results with the same time complexity — they differ only in direction.

**Memoization (top-down)** keeps the natural recursive structure, but caches each subproblem's result the first time it's computed, so repeat calls return instantly instead of recomputing.

```python
def fib_memo(n, memo=None):
    if memo is None:
        memo = {}
    if n in memo:
        return memo[n]
    if n <= 1:
        return n

    memo[n] = fib_memo(n - 1, memo) + fib_memo(n - 2, memo)
    return memo[n]

print(fib_memo(50))  # 12586269025 (instant, vs. fib_naive(50) which would take ages)
```

**Tabulation (bottom-up)** flips the direction: instead of recursing from the top down, it starts from the smallest subproblems and iteratively builds up to the answer, usually storing results in an array or table.

```python
def fib_tabulation(n):
    if n <= 1:
        return n

    table = [0] * (n + 1)
    table[1] = 1
    for i in range(2, n + 1):
        table[i] = table[i - 1] + table[i - 2]

    return table[n]
```

Both versions run in **O(n)** time and **O(n)** space — a massive improvement over the naive O(2ⁿ) approach. (Fibonacci specifically can be further optimized to O(1) space since each step only needs the previous two values, but the table version above generalizes better to harder DP problems.)

| | Memoization (top-down) | Tabulation (bottom-up) |
|---|---|---|
| Structure | Recursive, cached | Iterative, table-filling |
| Computes | Only the subproblems actually needed | Every subproblem, in order |
| Easier to write from | The natural recursive definition | A clear ordering of subproblems |
| Risk | Deep recursion can hit stack limits | Requires figuring out the fill order upfront |

---

## 6.3 Classic DP Problems

**0/1 Knapsack** — given items with weights and values, and a knapsack with a weight limit, choose the subset of items that maximizes total value without exceeding the limit.

```python
def knapsack(weights, values, capacity):
    n = len(weights)
    # dp[i][w] = best value using the first i items with capacity w
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        for w in range(capacity + 1):
            dp[i][w] = dp[i - 1][w]  # option: don't take item i-1
            if weights[i - 1] <= w:
                dp[i][w] = max(dp[i][w], dp[i - 1][w - weights[i - 1]] + values[i - 1])

    return dp[n][capacity]

print(knapsack([2, 3, 4, 5], [3, 4, 5, 6], 5))  # 7
```

**Coin Change (fewest coins, general case)** — unlike the greedy version from Lesson 5.2, this DP version works correctly for *any* set of denominations.

```python
def coin_change(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0

    for a in range(1, amount + 1):
        for coin in coins:
            if coin <= a:
                dp[a] = min(dp[a], dp[a - coin] + 1)

    return dp[amount] if dp[amount] != float('inf') else -1

print(coin_change([1, 3, 4], 6))  # 2  (3 + 3), correctly beating the greedy 4+1+1
```

**Longest Common Subsequence (LCS)** — find the longest sequence of characters that appears (in order, not necessarily contiguously) in both of two strings. Widely used in diff tools and DNA sequence comparison.

```python
def lcs(a, b):
    m, n = len(a), len(b)
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if a[i - 1] == b[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

    return dp[m][n]

print(lcs("ABCBDAB", "BDCABA"))  # 4  ("BCBA" or "BDAB")
```

Other classic DP problems worth knowing include the **Longest Increasing Subsequence**, **Edit Distance** (minimum operations to turn one string into another), and the **Matrix Chain Multiplication** problem. The general recipe for spotting a DP problem: look for a recursive brute-force solution, check whether it re-solves the same subproblem multiple times, and if so, cache the results.

[Previous](./[5]-Greedy-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[7]-Backtracking.md)
