[Previous](./[1]-Sorting-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[3]-Two-Pointers-And-Sliding-Window.md)

# Lesson 2 - Searching Algorithms

## 2.1 Linear Search

Linear search is the simplest possible way to find a value: check every element, one at a time, until you find it (or run out of elements).

```python
def linear_search(arr, target):
    for i, value in enumerate(arr):
        if value == target:
            return i
    return -1

print(linear_search([4, 2, 7, 1, 9], 7))  # 2
print(linear_search([4, 2, 7, 1, 9], 5))  # -1
```

Linear search runs in **O(n)** time — in the worst case you check every element. Its one big advantage is that it works on **any** list, sorted or not. There's no faster general-purpose way to search unsorted data; if you need faster lookups repeatedly, it's usually worth sorting the data first or using a different data structure (like a hash map) entirely.

---

## 2.2 Binary Search

Binary search takes advantage of **sorted** data. Instead of checking every element, it repeatedly checks the middle element and eliminates half the remaining search space each time.

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

print(binary_search([1, 3, 5, 7, 9, 11], 7))  # 3
```

A recursive version of the same idea:

```python
def binary_search_recursive(arr, target, low=0, high=None):
    if high is None:
        high = len(arr) - 1
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

Because it discards half the remaining elements on every step, binary search runs in **O(log n)** time — dramatically faster than linear search on large datasets. A binary search over a billion sorted elements takes at most about 30 comparisons. The catch: the data **must** be sorted first, and sorting itself costs O(n log n), so binary search only pays off when you're searching the same sorted data many times, or the data was already sorted for another reason.

---

## 2.3 Searching in Trees and Graphs

Searching isn't limited to flat arrays — trees and graphs need their own search strategies, since there's no single "middle" element to jump to.

**Binary Search Trees (BSTs)** extend the same halving idea from binary search into a tree structure: every node's left subtree holds smaller values and its right subtree holds larger values, so searching means comparing against the current node and moving left or right.

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def bst_search(node, target):
    if node is None or node.value == target:
        return node
    if target < node.value:
        return bst_search(node.left, target)
    return bst_search(node.right, target)
```

A balanced BST gives **O(log n)** search, matching binary search on an array, but supports fast insertion and deletion too — something a sorted array can't do efficiently.

For general **graphs** (and trees, which are just a special case of graphs), the two fundamental traversal strategies are:

- **Breadth-First Search (BFS)** — explore level by level, visiting all neighbors of a node before moving further out. Good for finding the shortest path in an unweighted graph.
- **Depth-First Search (DFS)** — explore as far as possible down one path before backtracking. Good for exploring all possibilities, detecting cycles, and problems like maze-solving.

```python
from collections import deque

def bfs(graph, start, target):
    visited = {start}
    queue = deque([start])

    while queue:
        node = queue.popleft()
        if node == target:
            return True
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    return False

def dfs(graph, start, target, visited=None):
    if visited is None:
        visited = set()
    if start == target:
        return True
    visited.add(start)
    for neighbor in graph[start]:
        if neighbor not in visited:
            if dfs(graph, neighbor, target, visited):
                return True
    return False
```

Both BFS and DFS run in **O(V + E)** time, where V is the number of vertices (nodes) and E is the number of edges — every node and edge is visited at most once. BFS and DFS are covered in much more depth, along with weighted shortest-path and spanning-tree algorithms, in Lesson 8 (Graph Algorithms).

[Previous](./[1]-Sorting-Algorithms.md) | [Table of Contents](./[0]-Introduction-to-Algorithms.md) | [Next](./[3]-Two-Pointers-And-Sliding-Window.md)
