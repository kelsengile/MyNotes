[Previous](./[4]-Recursion-And-Divide-And-Conquer.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[6]-Dynamic-Programming.md)

# Lesson 5 - Greedy Algorithms

## 5.1 The Greedy Choice Property

A **greedy algorithm** builds up a solution step by step, at each step making whichever choice looks best *right now*, without reconsidering earlier choices. It never backtracks.

Greedy algorithms only produce a correct, optimal answer when the problem has two properties:

- **Greedy choice property** — a globally optimal solution can be reached by making a locally optimal (best-looking-right-now) choice at each step.
- **Optimal substructure** — an optimal solution to the problem contains optimal solutions to its subproblems (this property is shared with dynamic programming, covered in Lesson 6).

Not every problem has these properties, and using a greedy approach on a problem that lacks them produces a solution that's fast to compute but **wrong** — often just an approximation of the true optimum. Part of learning to use greedy algorithms is learning to recognize (or prove) when the greedy choice actually leads to a global optimum, versus when it merely seems reasonable.

The appeal of greedy algorithms is speed: because there's no backtracking or re-evaluation, they're usually much faster than exploring every possibility, often running in O(n log n) (dominated by sorting the input) or even O(n).

---

## 5.2 Classic Examples (Coin Change, Interval Scheduling)

**Coin Change (greedy version)** — given a set of coin denominations, make a target amount using the fewest coins, by always picking the largest coin that doesn't exceed the remaining amount.

```python
def coin_change_greedy(coins, amount):
    coins = sorted(coins, reverse=True)
    result = []

    for coin in coins:
        while amount >= coin:
            amount -= coin
            result.append(coin)

    return result if amount == 0 else None

print(coin_change_greedy([25, 10, 5, 1], 63))  # [25, 25, 10, 1, 1, 1]
```

This greedy approach gives the optimal (fewest-coin) answer for denominations like US coins (1, 5, 10, 25), **but it is not guaranteed to work for every set of denominations**. For example, with coins `[1, 3, 4]` and a target of `6`, greedy picks `4 + 1 + 1` (3 coins), but the optimal answer is `3 + 3` (2 coins). This is exactly why the general coin change problem is solved with dynamic programming (Lesson 6) rather than a greedy approach — it's a case where the greedy choice property doesn't hold.

**Interval Scheduling** — given a list of tasks, each with a start and end time, select the maximum number of non-overlapping tasks. The greedy strategy: always pick the task that **finishes earliest** among the remaining valid options.

```python
def interval_scheduling(intervals):
    # sort by end time
    intervals = sorted(intervals, key=lambda x: x[1])
    selected = []
    last_end = float('-inf')

    for start, end in intervals:
        if start >= last_end:
            selected.append((start, end))
            last_end = end

    return selected

tasks = [(1, 3), (2, 5), (4, 6), (6, 8), (5, 7)]
print(interval_scheduling(tasks))  # [(1, 3), (4, 6), (6, 8)]
```

Unlike the general coin change problem, interval scheduling's greedy solution **is** provably optimal — sorting by end time and always taking the earliest-finishing valid task never blocks you from a better outcome, because any other valid task must finish at least as late.

Other classic greedy problems include **Huffman coding** (building an optimal prefix-free encoding for compression), **Dijkstra's shortest path algorithm** (Lesson 8.3), and **Kruskal's/Prim's algorithms** for building a minimum spanning tree (Lesson 8.4) — all cases where making the locally best choice at each step provably leads to the globally best result.

[Previous](./[4]-Recursion-And-Divide-And-Conquer.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[6]-Dynamic-Programming.md)
