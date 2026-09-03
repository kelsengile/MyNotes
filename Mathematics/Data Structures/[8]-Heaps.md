[Previous](./[7]-Trees.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[9]-Graphs.md)

# Lesson 8 - Heaps

A heap is a specialized tree that gives up the BST's full ordering guarantee in exchange for something narrower but faster to maintain: instant access to the single smallest (or largest) element. Where a BST asks "where does this value belong relative to everything else?", a heap only ever asks "what's currently on top?"

## 8.1 Min-Heaps and Max-Heaps

A heap is a binary tree with one rule, the **heap property**, instead of a BST's left/right ordering rule:

- **Min-heap**: every parent is smaller than or equal to both its children. The smallest element in the entire tree is always at the root.
- **Max-heap**: every parent is larger than or equal to both its children. The largest element is always at the root.

```
Min-Heap:              Max-Heap:
      1                     9
     / \                   / \
    3   2                 7   8
   / \                   / \
  5   4                 3   6
```

Notice there's no rule about left vs. right, and siblings have no defined relationship to each other — only the parent-child relationship matters. This looser rule is what makes heaps cheaper to maintain than a fully balanced BST.

A second key property: a heap is always a **complete binary tree** — every level is fully filled except possibly the last, which fills left to right with no gaps. This means a heap can be stored efficiently in a plain array with no pointers at all: for a node at index `i`, its children live at `2i + 1` and `2i + 2`, and its parent lives at `(i - 1) // 2`.

```python
# A min-heap [1, 3, 2, 5, 4] stored as a plain array,
# representing the tree drawn above.
```

## 8.2 Heap Operations (Insert, Extract)

**Insert**: add the new value at the next open leaf position (the end of the array), then **bubble up** ("sift up") — repeatedly swap it with its parent as long as it violates the heap property.

```python
import heapq

heap = []
heapq.heappush(heap, 5)
heapq.heappush(heap, 3)
heapq.heappush(heap, 8)
heapq.heappush(heap, 1)   # bubbles all the way to the root
print(heap[0])             # 1 — the min is always at index 0
```

**Extract** (remove the root): move the *last* element in the array into the root position, shrink the array by one, then **bubble down** ("sift down") — repeatedly swap the new root with its smaller child (min-heap) until the heap property is restored.

```python
heapq.heappop(heap)   # removes and returns the current minimum
```

Both insert and extract only ever touch one path from root to leaf (or leaf to root), and a complete binary tree with n nodes has height log₂(n) — so both operations run in **O(log n)**. Peeking at the root (`heap[0]`) is O(1), since it never requires any restructuring.

| Operation | Cost |
|---|---|
| Peek min/max | O(1) |
| Insert | O(log n) |
| Extract min/max | O(log n) |
| Build heap from n items | O(n) |

## 8.3 Heaps and Priority Queues

A heap is, in practice, *the* standard way to implement a **priority queue** (introduced in Lesson 4). Recall a priority queue's job: always serve the highest-priority element next, regardless of arrival order. A heap does exactly that — the root is always the current highest-priority (smallest, for a min-heap) element — and does it in O(log n) per operation, far better than the O(n) a naive "always scan for the highest priority" approach would cost.

```python
import heapq

tasks = []
heapq.heappush(tasks, (1, "fix critical outage"))
heapq.heappush(tasks, (3, "update documentation"))
heapq.heappush(tasks, (2, "review pull request"))

while tasks:
    priority, task = heapq.heappop(tasks)
    print(f"{priority}: {task}")
# 1: fix critical outage
# 2: review pull request
# 3: update documentation
```

**Common use cases**: priority queues and task scheduling (as above), the classic **heapsort** algorithm (repeatedly extract the min/max to produce a sorted output in O(n log n)), finding the k-largest or k-smallest elements in a collection without fully sorting it, and shortest-path algorithms like Dijkstra's, which repeatedly need "the unvisited node with the smallest known distance so far" — precisely the operation a heap is built for.

[Previous](./[7]-Trees.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[9]-Graphs.md)
