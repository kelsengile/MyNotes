[Previous](./[3]-Stacks.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[5]-Hash-Tables.md)

# Lesson 4 - Queues

A queue is the mirror image of a stack: instead of restricting access to one end, it restricts *insertion* to one end and *removal* to the other. That single change flips the ordering guarantee entirely, and makes queues the natural fit for anything that needs to process items in the order they arrived.

## 4.1 The FIFO Principle

A queue follows **FIFO**: First In, First Out. Think of a line at a checkout counter — the first person to join the line is the first person served, and new people join at the back.

A queue supports:

- **enqueue(value)** — add an element to the back.
- **dequeue()** — remove and return the element at the front.
- **peek() / front()** — look at the front element without removing it.
- **isEmpty()** — check whether the queue has anything in it.

```python
from collections import deque

queue = deque()
queue.append(1)        # enqueue → [1]
queue.append(2)        # enqueue → [1, 2]
queue.append(3)        # enqueue → [1, 2, 3]
queue.popleft()        # dequeue → returns 1, queue is [2, 3]
```

Note the implementation detail: a plain Python list works fine for a stack (popping the end is O(1)), but using `list.pop(0)` to dequeue from the *front* is O(n), since every remaining element has to shift left. That's why `collections.deque` — a doubly linked list under the hood — is the right tool: it gives O(1) operations at *both* ends.

## 4.2 Circular Queues

If you implement a queue on top of a fixed-size array naively, dequeuing from the front either costs O(n) (shifting everything left) or wastes memory (leaving a growing gap at the front that's never reused).

A **circular queue** (or ring buffer) solves this by treating the array as if it wraps around: after the last index, the next position loops back to index 0. Two pointers, `front` and `rear`, track the logical ends of the queue and move forward (wrapping around) as elements are enqueued and dequeued, so no shifting is ever needed and the freed-up space at the front gets reused automatically.

```python
class CircularQueue:
    def __init__(self, capacity):
        self._data = [None] * capacity
        self._capacity = capacity
        self._front = 0
        self._size = 0

    def enqueue(self, value):
        if self._size == self._capacity:
            raise OverflowError("queue is full")
        rear = (self._front + self._size) % self._capacity
        self._data[rear] = value
        self._size += 1                     # O(1)

    def dequeue(self):
        if self._size == 0:
            raise IndexError("queue is empty")
        value = self._data[self._front]
        self._front = (self._front + 1) % self._capacity
        self._size -= 1
        return value                        # O(1)
```

Circular queues are common in fixed-memory environments like embedded systems and streaming buffers, where you want strict O(1) enqueue/dequeue without ever resizing.

## 4.3 Deques and Priority Queues

A **deque** ("double-ended queue", pronounced "deck") relaxes the FIFO restriction entirely — you can push and pop from *both* ends in O(1). A deque can act as a stack, a queue, or both at once, which is why it's often the default building block language libraries provide instead of separate stack and queue types.

```python
dq = deque()
dq.append(1)         # add to back
dq.appendleft(0)      # add to front
dq.pop()               # remove from back
dq.popleft()           # remove from front
```

A **priority queue** abandons arrival order altogether: instead of "first in, first out," every element has an associated priority, and `dequeue`/`pop` always returns the highest (or lowest) priority element regardless of when it was added. Priority queues are usually implemented with a **heap** (covered in Lesson 8), giving O(log n) insert and O(log n) removal of the top-priority item — much faster than the O(n) scan a naive priority-sorted list would require.

```python
import heapq

pq = []
heapq.heappush(pq, (2, "medium task"))
heapq.heappush(pq, (1, "urgent task"))
heapq.heappush(pq, (3, "low priority task"))
heapq.heappop(pq)   # (1, "urgent task") — lowest number = highest priority
```

**Common use cases across the queue family**: task scheduling, breadth-first search (BFS — explored in Lesson 9), print job queues, request handling in web servers, and simulations where events must be processed in a controlled order (arrival order for plain queues, importance order for priority queues).

[Previous](./[3]-Stacks.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[5]-Hash-Tables.md)
