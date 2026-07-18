# Algorithms & Problem Solving

## 16.1 Time and Space Complexity (Big O)

Big O notation describes how an algorithm's running time or memory usage grows as the input size (`n`) grows. It captures the **worst-case growth rate**, ignoring constant factors and lower-order terms, so different algorithms can be compared independent of hardware or implementation details.

### Why It Matters

An algorithm that's fast for small inputs can become unusable at scale. Big O helps predict how code will behave before it's ever run against real-world data sizes.

### Common Complexity Classes (Best to Worst)

| Notation | Name | Example |
|---|---|---|
| `O(1)` | Constant | Array index access, hash table lookup |
| `O(log n)` | Logarithmic | Binary search |
| `O(n)` | Linear | Single loop through an array |
| `O(n log n)` | Linearithmic | Efficient sorting (merge sort, quicksort avg.) |
| `O(n²)` | Quadratic | Nested loops (bubble sort, naive duplicate check) |
| `O(n³)` | Cubic | Triple nested loops (naive matrix multiplication) |
| `O(2ⁿ)` | Exponential | Naive recursive Fibonacci, brute-force subset generation |
| `O(n!)` | Factorial | Brute-force traveling salesman, generating all permutations |

```
Growth rate at n = 10 vs n = 1,000:

O(1)         1           1
O(log n)     ~3          ~10
O(n)         10          1,000
O(n log n)   ~33         ~10,000
O(n²)        100         1,000,000
O(2ⁿ)        1,024       (astronomically large)
```

### Determining Complexity

```python
def example(arr):
    total = 0
    for x in arr:          # O(n)
        total += x
    return total            # Overall: O(n)

def example2(arr):
    for x in arr:            # O(n)
        for y in arr:        # O(n)
            print(x, y)       # Overall: O(n * n) = O(n²)

def example3(arr):
    if len(arr) == 0:
        return None
    return arr[0]            # O(1) — no loop, constant work
```

**Rules of thumb:**
- Drop constants: `O(2n)` → `O(n)`.
- Drop lower-order terms: `O(n² + n)` → `O(n²)`.
- Different inputs get different variables: comparing two separate arrays of size `n` and `m` in nested loops is `O(n * m)`, not `O(n²)`.
- Sequential (non-nested) operations **add**: a loop of `O(n)` followed by another loop of `O(n)` is `O(n) + O(n) = O(n)`, not `O(n²)`.
- Nested operations **multiply**: a loop inside a loop multiplies their complexities.

### Best, Average, and Worst Case

An algorithm can have different complexities depending on the input:
- **Best case** — the most favorable input (e.g., searching for the first element in a list).
- **Average case** — expected performance across typical inputs.
- **Worst case** — the least favorable input (this is what Big O most commonly describes).

Example: **Quicksort** averages `O(n log n)` but degrades to `O(n²)` in the worst case (e.g., an already-sorted array with a poorly chosen pivot).

### Space Complexity

Measures memory usage as a function of input size, following the same notation.

```python
def sum_array(arr):        # O(1) space — only a few variables, regardless of input size
    total = 0
    for x in arr:
        total += x
    return total

def duplicate_array(arr):   # O(n) space — creates a new array proportional to input size
    return arr + arr
```

There's often a **time-space trade-off**: using more memory (e.g., a hash map for lookups) can reduce time complexity, and vice versa.

## 16.2 Sorting Algorithms

Sorting arranges elements into a defined order (typically ascending/descending) and is a foundational building block for many other algorithms (binary search, deduplication, etc.).

### Simple Sorts — O(n²)

**Bubble Sort** — repeatedly swaps adjacent out-of-order elements until the list is sorted.
```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
    return arr
```

**Selection Sort** — repeatedly selects the minimum remaining element and places it at the front.
```python
def selection_sort(arr):
    n = len(arr)
    for i in range(n):
        min_idx = i
        for j in range(i + 1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    return arr
```

**Insertion Sort** — builds a sorted portion of the array one element at a time, inserting each new element into its correct position. Efficient for small or nearly-sorted data.
```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key
    return arr
```

### Efficient Sorts — O(n log n)

**Merge Sort** — a divide-and-conquer algorithm: splits the array in half recursively, sorts each half, then merges the sorted halves. Stable and predictable, always `O(n log n)`, but uses `O(n)` extra space.
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result, i, j = [], 0, 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i]); i += 1
        else:
            result.append(right[j]); j += 1
    return result + left[i:] + right[j:]
```

**Quicksort** — picks a "pivot," partitions elements into less-than/greater-than the pivot, then recursively sorts each partition. Fast in practice (in-place, good cache behavior) but `O(n²)` worst case with a bad pivot choice.
```python
def quicksort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    mid = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quicksort(left) + mid + quicksort(right)
```

**Heapsort** — builds a max-heap from the data, then repeatedly extracts the maximum element. Guaranteed `O(n log n)`, in-place, but not stable.

### Comparison Table

| Algorithm | Best | Average | Worst | Space | Stable? |
|---|---|---|---|---|---|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quicksort | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heapsort | O(n log n) | O(n log n) | O(n log n) | O(1) | No |

*Stability means elements with equal keys retain their original relative order — important when sorting by one field while preserving prior ordering by another.*

### Non-Comparison Sorts

For special cases, sorts that don't compare elements pairwise can beat `O(n log n)`:
- **Counting Sort** — `O(n + k)`, works when values are integers within a known, limited range `k`.
- **Radix Sort** — `O(d * (n + k))`, sorts by processing digits/characters position by position.

### Practical Notes

- Most language standard libraries (Python's `sorted()`, Java's `Collections.sort()`) use **hybrid algorithms** (e.g., Timsort — a mix of merge sort and insertion sort) tuned for real-world data.
- For small arrays (roughly n < 20), simple `O(n²)` sorts can outperform `O(n log n)` sorts due to lower constant overhead — this is why hybrid sorts often fall back to insertion sort for small partitions.

## 16.3 Searching Algorithms

### Linear Search — O(n)

Checks each element one at a time until a match is found. Works on unsorted data.

```python
def linear_search(arr, target):
    for i, val in enumerate(arr):
        if val == target:
            return i
    return -1
```

### Binary Search — O(log n)

Requires a **sorted** array. Repeatedly halves the search space by comparing the target to the middle element.

```python
def binary_search(arr, target):
    low, high = 0, len(arr) - 1
    while low <= high:
        mid = (low + high) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            low = mid + 1
        else:
            high = mid - 1
    return -1
```

```python
# Recursive version
def binary_search_recursive(arr, target, low, high):
    if low > high:
        return -1
    mid = (low + high) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        return binary_search_recursive(arr, target, mid + 1, high)
    else:
        return binary_search_recursive(arr, target, low, mid - 1)
```

**Why it's fast:** each comparison eliminates half the remaining search space, so it takes at most `log₂(n)` comparisons — searching a billion sorted items takes at most ~30 comparisons.

### Hash-Based Search — O(1) average

Using a hash table (dict/map), lookups by key are constant time on average, at the cost of extra memory and no inherent ordering.

```python
lookup = {val: i for i, val in enumerate(arr)}
index = lookup.get(target, -1)   # O(1) average
```

### Tree-Based Search

- **Binary Search Trees (BST)** — `O(log n)` average for search/insert/delete, degrading to `O(n)` if the tree becomes unbalanced (essentially a linked list).
- **Self-balancing trees** (AVL, Red-Black trees) — guarantee `O(log n)` by automatically rebalancing after insertions/deletions.
- **B-Trees** — used heavily in databases and filesystems for efficient on-disk search with large branching factors.

### Graph Search: BFS and DFS

**Breadth-First Search (BFS)** — explores neighbors level by level using a queue; finds the shortest path in unweighted graphs.
```python
from collections import deque

def bfs(graph, start):
    visited, queue, order = {start}, deque([start]), []
    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    return order
```

**Depth-First Search (DFS)** — explores as far as possible down one branch before backtracking, using a stack (or recursion).
```python
def dfs(graph, start, visited=None, order=None):
    if visited is None:
        visited, order = set(), []
    visited.add(start)
    order.append(start)
    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited, order)
    return order
```

| | BFS | DFS |
|---|---|---|
| Data structure | Queue | Stack (or recursion) |
| Finds shortest path (unweighted)? | Yes | No |
| Memory usage | Can be high (stores a full "frontier") | Generally lower |
| Common uses | Shortest path, level-order traversal | Cycle detection, topological sort, maze solving |

### Choosing a Search Strategy

| Situation | Best Choice |
|---|---|
| Unsorted small dataset | Linear search |
| Sorted array, static data | Binary search |
| Frequent lookups by key | Hash table |
| Range queries, ordered traversal | Balanced BST |
| Shortest path in unweighted graph | BFS |
| Explore all paths / detect cycles | DFS |

## 16.4 Recursion & Divide-and-Conquer

### Recursion Basics

A recursive function calls itself to solve smaller instances of the same problem. Every correct recursive function needs:

1. **Base case(s)** — the condition(s) under which the function stops recursing and returns directly.
2. **Recursive case** — where the function calls itself with a smaller/simpler input, making progress toward the base case.

```python
def factorial(n):
    if n <= 1:          # base case
        return 1
    return n * factorial(n - 1)   # recursive case
```

**Without a reachable base case, recursion leads to infinite calls and a stack overflow.**

### The Call Stack

Each recursive call adds a new **stack frame** holding that call's local variables and return address. Deep recursion can exhaust the call stack (`RecursionError` in Python, `StackOverflowError` in Java).

```
factorial(4)
 └─ factorial(3)
     └─ factorial(2)
         └─ factorial(1) → returns 1
     └─ returns 2 * 1 = 2
 └─ returns 3 * 2 = 6
returns 4 * 6 = 24
```

### Tail Recursion

A recursive call is in **tail position** if it's the last operation in the function, with no pending work after it returns. Some languages/compilers optimize tail calls to avoid growing the stack (**tail call optimization**), though Python notably does not.

```python
# Not tail-recursive: multiplication happens AFTER the recursive call returns
def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n - 1)

# Tail-recursive: the recursive call is the final action, using an accumulator
def factorial_tail(n, acc=1):
    if n <= 1:
        return acc
    return factorial_tail(n - 1, acc * n)
```

### Divide-and-Conquer

A powerful algorithm design paradigm built on three steps:

1. **Divide** — split the problem into smaller subproblems of the same type.
2. **Conquer** — solve each subproblem recursively (or directly, if small enough).
3. **Combine** — merge the subproblem solutions into a solution for the original problem.

**Classic examples:** merge sort (divide array in half, sort each, merge), quicksort (partition, recurse), binary search (halve the search space), and the Fast Fourier Transform.

```python
# Divide-and-conquer: finding the maximum value in an array
def find_max(arr, low, high):
    if low == high:                     # base case: single element
        return arr[low]
    mid = (low + high) // 2
    left_max = find_max(arr, low, mid)       # conquer left half
    right_max = find_max(arr, mid + 1, high) # conquer right half
    return max(left_max, right_max)           # combine
```

### Recurrence Relations

Divide-and-conquer complexity is often expressed as a recurrence relation, e.g. merge sort's `T(n) = 2T(n/2) + O(n)`, which resolves to `O(n log n)` — captured generally by the **Master Theorem**.

### Recursion vs. Iteration

| | Recursion | Iteration |
|---|---|---|
| Readability | Often clearer for naturally recursive problems (trees, divide-and-conquer) | Often clearer for simple repeated steps |
| Memory | Uses call stack — risk of stack overflow on deep recursion | Constant stack usage |
| Performance | Function call overhead | Typically faster, no call overhead |
| Best for | Tree/graph traversal, backtracking, divide-and-conquer | Simple loops, performance-critical code |

Any recursive algorithm can be rewritten iteratively (often using an explicit stack to mimic the call stack), and vice versa — the choice is about clarity and constraints, not raw capability.

### Common Pitfalls

- **Missing or unreachable base case** → infinite recursion → stack overflow.
- **Not making progress** toward the base case (e.g., forgetting to decrement a counter).
- **Redundant recomputation** — naive recursive Fibonacci recalculates the same subproblems exponentially many times; fix with **memoization** (caching results) or **dynamic programming**.

```python
# Naive - O(2ⁿ)
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)

# Memoized - O(n)
def fib_memo(n, cache={}):
    if n in cache:
        return cache[n]
    if n <= 1:
        return n
    cache[n] = fib_memo(n - 1, cache) + fib_memo(n - 2, cache)
    return cache[n]
```

## 16.5 Problem-Solving Strategies

Beyond knowing individual algorithms, developing a systematic approach to unfamiliar problems is what separates effective problem-solvers.

### A General Framework

1. **Understand the problem** — restate it in your own words; identify inputs, outputs, and constraints. Ask about edge cases (empty input, duplicates, negative numbers) if unclear.
2. **Work through examples** — manually trace small examples, including edge cases, to build intuition before writing any code.
3. **Devise a plan** — sketch an approach in pseudocode or plain language before implementing. Identify which known pattern (if any) applies.
4. **Implement** — translate the plan into code, keeping it simple and readable at first.
5. **Test and verify** — check against your worked examples, edge cases, and (if relevant) large inputs for performance.
6. **Reflect and optimize** — once correct, consider whether time/space complexity can be improved.

This general process (understand → plan → execute → review) is adapted from George Pólya's classic approach to mathematical problem-solving.

### Common Algorithmic Patterns

Recognizing recurring patterns often points directly to an efficient solution:

- **Two Pointers** — using two indices moving through a structure (often from opposite ends or at different speeds) to avoid nested loops. Useful for sorted arrays, palindrome checks, and cycle detection (e.g., Floyd's algorithm).
- **Sliding Window** — maintaining a "window" of elements over an array/string that expands/contracts, useful for subarray/substring problems (e.g., "longest substring without repeating characters").
- **Fast & Slow Pointers** — two pointers moving at different speeds through a linked structure, commonly used to detect cycles.
- **Hash Map for Lookups** — trading space for time by storing seen values for `O(1)` lookup instead of nested loops (e.g., "two sum" problem: `O(n)` with a hash map vs. `O(n²)` brute force).
- **Backtracking** — incrementally building candidate solutions and abandoning ("backtracking" from) ones that can't possibly succeed — used for permutations, combinations, N-Queens, Sudoku solvers.
- **Dynamic Programming (DP)** — breaking a problem into overlapping subproblems, solving each once, and storing (memoizing) the results; applicable when a problem has both *optimal substructure* and *overlapping subproblems* (e.g., knapsack, longest common subsequence, coin change).
- **Greedy Algorithms** — making the locally optimal choice at each step, hoping it leads to a global optimum; works for problems with the *greedy-choice property* (e.g., activity selection, Huffman coding) but not universally — always verify greedy correctness before relying on it.
- **Divide-and-Conquer** — as covered in 16.4, splitting into independent subproblems.
- **Graph Traversal (BFS/DFS)** — for problems modeled as networks of connections (social graphs, maps, dependency graphs).

### Complexity-Driven Thinking

Before coding, estimate the acceptable complexity from the problem's constraints:

| Input size (n) | Roughly acceptable complexity |
|---|---|
| n ≤ ~10 | O(n!) or O(2ⁿ) may be fine |
| n ≤ ~20-25 | O(2ⁿ) |
| n ≤ ~1,000 | O(n²) or O(n³) |
| n ≤ ~100,000 | O(n log n) |
| n ≤ ~10,000,000+ | O(n) or O(log n) |

If a brute-force `O(n²)` approach is too slow for the given constraints, that's a strong signal to look for a hash map, sorting, two-pointer, or DP-based optimization.

### Debugging Your Own Logic

- **Trace through examples by hand** before assuming the code is wrong — sometimes the *approach* itself is flawed, not the implementation.
- **Test edge cases explicitly**: empty input, single element, all-duplicate values, already-sorted/reverse-sorted data, very large/small numbers.
- **Simplify** — if stuck, solve a smaller or simpler version of the problem first, then generalize.
- **Verify time complexity assumptions** — profile or reason through nested loops/recursive calls to make sure the actual complexity matches what you intended.

### Practice Habits That Compound

- Focus on **recognizing patterns** rather than memorizing individual solutions — many problems are variations of a small set of core patterns.
- After solving a problem, **review alternative approaches** and their trade-offs, even if your first solution worked.
- Revisit problems after time has passed to check whether the pattern-recognition has become more automatic.
- When stuck for a long time, look at the underlying concept (not just the answer) and re-derive the solution yourself — this builds transferable understanding better than copying code.