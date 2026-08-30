[Previous](./[3]-Two-Pointers-And-Sliding-Window.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[5]-Greedy-Algorithms.md)

# Lesson 4 - Recursion And Divide-And-Conquer

## 4.1 The Divide-and-Conquer Strategy

Divide-and-conquer is a problem-solving strategy built on **recursion** — a function that calls itself on a smaller version of the same problem. Every divide-and-conquer algorithm follows the same three steps:

1. **Divide** — split the problem into smaller subproblems of the same type.
2. **Conquer** — solve each subproblem recursively. If a subproblem is small enough (the **base case**), solve it directly instead of recursing further.
3. **Combine** — merge the subproblem solutions into a solution for the original problem.

Every recursive function needs a base case, or it will recurse forever (and eventually crash with a stack overflow). A simple example — computing a factorial:

```python
def factorial(n):
    if n <= 1:          # base case
        return 1
    return n * factorial(n - 1)   # divide into a smaller subproblem

print(factorial(5))  # 120
```

Here there's no real "combine" step beyond multiplying, but the shape is the same: reduce the problem, solve the smaller version, and use that result to build the answer.

Divide-and-conquer is powerful because splitting a problem in half repeatedly tends to produce a logarithmic number of levels of recursion, which is where the common **O(n log n)** running time of algorithms like merge sort comes from. This can be seen with the **Master Theorem**, a formula for analyzing the time complexity of divide-and-conquer recurrences of the form `T(n) = a·T(n/b) + f(n)`, where `a` is the number of subproblems, `n/b` is the size of each subproblem, and `f(n)` is the cost of the divide/combine steps. Understanding the exact theorem isn't necessary to use divide-and-conquer effectively — the important intuition is that splitting work in half at each level, combined with cheap combine steps, tends to beat approaches that don't reduce the problem size at all.

---

## 4.2 Classic Examples (Merge Sort, Binary Search)

**Merge Sort** (introduced in Lesson 1.2) is the textbook divide-and-conquer algorithm:

- **Divide** — split the array into two halves.
- **Conquer** — recursively sort each half.
- **Combine** — merge the two sorted halves back into one sorted array.

```python
def merge_sort(arr):
    if len(arr) <= 1:          # base case: a list of 0 or 1 elements is already sorted
        return arr

    mid = len(arr) // 2
    left = merge_sort(arr[:mid])    # divide + conquer (left half)
    right = merge_sort(arr[mid:])   # divide + conquer (right half)

    return merge(left, right)       # combine
```

**Binary Search** (introduced in Lesson 2.2) also fits the pattern, though its "combine" step is trivial since only one half ever needs to be explored:

- **Divide** — look at the middle element and compare it to the target.
- **Conquer** — recurse into whichever half could contain the target (the other half is discarded entirely).
- **Combine** — nothing to combine; the answer from the recursive call *is* the answer.

```python
def binary_search(arr, target, low=0, high=None):
    if high is None:
        high = len(arr) - 1
    if low > high:               # base case: search space is empty
        return -1

    mid = (low + high) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] < target:
        return binary_search(arr, target, mid + 1, high)   # recurse right half
    else:
        return binary_search(arr, target, low, mid - 1)    # recurse left half
```

Other well-known divide-and-conquer algorithms include quick sort (Lesson 1.3), finding the maximum subarray sum, and multiplying large numbers or matrices faster than the naive approach (e.g. Karatsuba multiplication and Strassen's algorithm). The pattern to recognize: whenever a problem can be split into independent smaller versions of itself whose results can be cheaply combined, divide-and-conquer is worth considering.

**A note on recursion vs. iteration:** any recursive algorithm can, in principle, be rewritten iteratively using an explicit stack, and this is sometimes done to avoid recursion-depth limits or function-call overhead. But recursion is usually far easier to read and reason about for divide-and-conquer problems, since the code directly mirrors the divide/conquer/combine structure.

[Previous](./[3]-Two-Pointers-And-Sliding-Window.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[5]-Greedy-Algorithms.md)
