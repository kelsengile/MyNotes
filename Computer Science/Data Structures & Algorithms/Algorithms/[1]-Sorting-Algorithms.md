[Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[2]-Searching-Algorithms.md)

# Lesson 1 - Sorting Algorithms

## 1.1 Bubble, Selection, and Insertion Sort

These three are the simplest sorting algorithms. They're rarely used in production (real languages use faster algorithms under the hood), but they're the best place to build intuition for how sorting works and why time complexity matters.

**Bubble Sort** repeatedly walks through the list, comparing each pair of neighbors and swapping them if they're in the wrong order. Each full pass "bubbles" the largest remaining value to the end.

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        swapped = False
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True
        if not swapped:  # already sorted, stop early
            break
    return arr

print(bubble_sort([5, 2, 9, 1, 5, 6]))  # [1, 2, 5, 5, 6, 9]
```

**Selection Sort** finds the smallest remaining element and swaps it into its correct position, one slot at a time.

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

**Insertion Sort** builds the sorted list one item at a time, inserting each new element into its correct position among the already-sorted portion — much like sorting a hand of playing cards.

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

All three run in **O(n²)** time in the worst case, and **O(1)** extra space (they sort "in place"). Insertion sort is the fastest of the three on nearly-sorted data, running close to O(n).

---

## 1.2 Merge Sort

Merge sort is a **divide-and-conquer** algorithm (see Lesson 4). It splits the array in half recursively until each piece has one element, then merges the pieces back together in sorted order.

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])

    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result

print(merge_sort([5, 2, 9, 1, 5, 6]))  # [1, 2, 5, 5, 6, 9]
```

Merge sort always runs in **O(n log n)** time, no matter how the input is arranged — this predictability is its biggest strength. Its main downside is that it needs **O(n)** extra space for the merge step, so it isn't in-place.

---

## 1.3 Quick Sort

Quick sort also uses divide-and-conquer, but instead of splitting evenly, it picks a **pivot** element and partitions the array so everything smaller than the pivot ends up to its left and everything larger ends up to its right. It then recursively sorts each side.

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr

    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]

    return quick_sort(left) + middle + quick_sort(right)

print(quick_sort([5, 2, 9, 1, 5, 6]))  # [1, 2, 5, 5, 6, 9]
```

Quick sort runs in **O(n log n)** on average, which in practice tends to beat merge sort due to better cache performance and lower constant overhead. Its worst case is **O(n²)**, which happens when the pivot choice repeatedly produces very unbalanced partitions (e.g. always picking the smallest or largest element on an already-sorted array). Randomizing the pivot choice makes the worst case extremely unlikely in practice.

---

## 1.4 Heap Sort

Heap sort uses a **binary heap** (see the Data Structures topic) to sort. It first builds a max-heap out of the array, then repeatedly removes the largest element from the heap and places it at the end of the array.

```python
def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2

    if left < n and arr[left] > arr[largest]:
        largest = left
    if right < n and arr[right] > arr[largest]:
        largest = right

    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)

def heap_sort(arr):
    n = len(arr)

    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)

    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)

    return arr
```

Heap sort guarantees **O(n log n)** in every case (best, average, and worst) and sorts in place with **O(1)** extra space. It's slightly slower in practice than quick sort due to poor cache locality, but its guaranteed worst case makes it valuable when predictability matters more than raw speed.

---

## 1.5 Comparing Sorting Algorithms

| Algorithm      | Best        | Average     | Worst       | Space    | Stable? | In-place? |
|----------------|-------------|-------------|-------------|----------|---------|-----------|
| Bubble Sort    | O(n)        | O(n²)       | O(n²)       | O(1)     | Yes     | Yes       |
| Selection Sort | O(n²)       | O(n²)       | O(n²)       | O(1)     | No      | Yes       |
| Insertion Sort | O(n)        | O(n²)       | O(n²)       | O(1)     | Yes     | Yes       |
| Merge Sort     | O(n log n)  | O(n log n)  | O(n log n)  | O(n)     | Yes     | No        |
| Quick Sort     | O(n log n)  | O(n log n)  | O(n²)       | O(log n) | No      | Yes       |
| Heap Sort      | O(n log n)  | O(n log n)  | O(n log n)  | O(1)     | No      | Yes       |

A few rules of thumb for picking one:

- **Small or nearly-sorted arrays** → insertion sort. Its low overhead makes it fast in practice despite the O(n²) worst case, which is why many standard library sorts fall back to it for small sub-arrays.
- **Need a guaranteed worst case** (e.g. real-time systems) → merge sort or heap sort.
- **General-purpose, average case matters most** → quick sort, which is what most language standard libraries historically used (often a hybrid, switching to insertion sort for small partitions).
- **Stability matters** (equal elements must keep their original relative order — important when sorting by one key after already sorting by another) → merge sort or insertion sort. Quick sort and heap sort are not stable by default.

[Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[2]-Searching-Algorithms.md)
