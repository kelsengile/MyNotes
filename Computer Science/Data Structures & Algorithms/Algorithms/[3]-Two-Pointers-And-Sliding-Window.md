[Previous](./[2]-Searching-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[4]-Recursion-And-Divide-And-Conquer.md)

# Lesson 3 - Two Pointers And Sliding Window

## 3.1 The Two Pointers Technique

The two pointers technique uses two index variables that move through a data structure (usually an array or string) instead of one, often starting from opposite ends or moving at different speeds. It's a way to avoid nested loops — turning an O(n²) brute-force scan into an O(n) pass.

A classic example: given a **sorted** array, find two numbers that add up to a target value.

```python
def two_sum_sorted(arr, target):
    left, right = 0, len(arr) - 1

    while left < right:
        current_sum = arr[left] + arr[right]
        if current_sum == target:
            return (left, right)
        elif current_sum < target:
            left += 1   # sum too small, need a bigger number
        else:
            right -= 1  # sum too big, need a smaller number

    return None

print(two_sum_sorted([1, 2, 4, 7, 11, 15], 15))  # (2, 4) -> 4 + 11
```

Because `left` and `right` only ever move toward each other and never backtrack, this makes a single pass through the array — **O(n)** time, **O(1)** extra space, compared to the O(n²) brute-force approach of checking every pair.

Another common pattern moves both pointers in the **same direction** at different speeds — for example, detecting a cycle in a linked list ("Floyd's tortoise and hare"), or removing duplicates from a sorted array in place:

```python
def remove_duplicates(arr):
    if not arr:
        return 0

    slow = 0
    for fast in range(1, len(arr)):
        if arr[fast] != arr[slow]:
            slow += 1
            arr[slow] = arr[fast]

    return slow + 1  # new length of the deduplicated portion
```

---

## 3.2 The Sliding Window Technique

Sliding window is a specialized form of two pointers used for problems about **contiguous subarrays or substrings**. Instead of recalculating a sum or count from scratch for every possible subarray, you maintain a "window" between two pointers and slide it across the data, adding one element as the window grows and removing one as it shrinks.

**Fixed-size window** — find the maximum sum of any subarray of size `k`:

```python
def max_sum_subarray(arr, k):
    window_sum = sum(arr[:k])
    max_sum = window_sum

    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i - k]  # add new element, drop oldest
        max_sum = max(max_sum, window_sum)

    return max_sum

print(max_sum_subarray([2, 1, 5, 1, 3, 2], 3))  # 9  (5 + 1 + 3)
```

**Variable-size window** — find the length of the smallest contiguous subarray with a sum ≥ target:

```python
def smallest_subarray_sum(arr, target):
    left = 0
    current_sum = 0
    min_length = float('inf')

    for right in range(len(arr)):
        current_sum += arr[right]
        while current_sum >= target:
            min_length = min(min_length, right - left + 1)
            current_sum -= arr[left]
            left += 1

    return min_length if min_length != float('inf') else 0

print(smallest_subarray_sum([2, 1, 5, 2, 3, 2], 7))  # 2  ([5, 2])
```

Both versions avoid recomputing the sum of every possible subarray from scratch, which is what makes sliding window **O(n)** instead of the O(n²) (or worse) brute-force approach of checking every start and end position.

---

## 3.3 When to Reach for Each Pattern

| Signal in the problem                                          | Likely pattern      |
|------------------------------------------------------------------|----------------------|
| Sorted array, looking for a pair/triplet matching a condition   | Two pointers (opposite ends) |
| Linked list, need to detect a cycle or find a middle node        | Two pointers (fast/slow) |
| "Contiguous subarray/substring" + fixed size `k`                 | Sliding window (fixed) |
| "Contiguous subarray/substring" + "longest/shortest that satisfies..." | Sliding window (variable) |
| Need to compare elements from both ends of a structure           | Two pointers (opposite ends) |
| Unsorted data with no contiguous/subarray requirement             | Neither — consider hashing or sorting first |

A good rule of thumb: if the brute-force solution is a nested loop where the inner loop's work only ever grows or shrinks in one direction as the outer loop advances (rather than restarting from scratch), that nested loop can usually be collapsed into a single pass with two pointers or a sliding window.

[Previous](./[2]-Searching-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[4]-Recursion-And-Divide-And-Conquer.md)
