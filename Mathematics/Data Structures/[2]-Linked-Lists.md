[Previous](./[1]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[3]-Stacks.md)

# Lesson 2 - Linked Lists

A linked list stores elements in **nodes** that are scattered anywhere in memory, with each node holding a pointer (or reference) to the next one. Unlike an array, there's no contiguous block and no arithmetic shortcut to a given position — you have to follow the chain of pointers one node at a time. That trade-off is the whole story of linked lists: you give up fast random access in exchange for cheap insertion and deletion.

## 2.1 Singly Linked Lists

Each node in a **singly linked list** holds two things: a value, and a pointer to the *next* node. The list itself just keeps a reference to the first node, called the **head**. The last node's `next` pointer is `null`/`None`, marking the end of the list.

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None

class SinglyLinkedList:
    def __init__(self):
        self.head = None

    def push_front(self, value):
        new_node = Node(value)
        new_node.next = self.head
        self.head = new_node   # O(1) — no shifting required

    def find(self, value):
        current = self.head
        while current:
            if current.value == value:
                return current
            current = current.next
        return None            # O(n) — must walk the chain
```

Because you only ever have direct access to the head, reaching the *tail* or any middle node means walking through every node before it — that's O(n) just to arrive at a position, even before doing anything with it.

## 2.2 Doubly Linked Lists

A **doubly linked list** gives each node a second pointer, `previous`, pointing back to the node before it. This lets you traverse the list in both directions and — crucially — delete a node in O(1) *if you already have a reference to it*, since you no longer need to walk from the head to find its predecessor.

```python
class DNode:
    def __init__(self, value):
        self.value = value
        self.next = None
        self.prev = None

class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None

    def push_back(self, value):
        new_node = DNode(value)
        if not self.tail:
            self.head = self.tail = new_node
        else:
            new_node.prev = self.tail
            self.tail.next = new_node
            self.tail = new_node   # O(1), thanks to the tail pointer
```

The cost of the extra flexibility is memory: every node needs a second pointer, and every insert/delete has twice as many pointers to update correctly.

## 2.3 Circular Linked Lists

A **circular linked list** connects the last node back to the first instead of pointing to `null`. This works with either a singly or doubly linked structure. There's no fixed "end," which makes circular lists a natural fit for anything that cycles repeatedly — a round-robin CPU scheduler, a music playlist on repeat, or a multiplayer game rotating through players' turns.

```python
class CircularNode:
    def __init__(self, value):
        self.value = value
        self.next = None

def build_circular(values):
    head = CircularNode(values[0])
    current = head
    for v in values[1:]:
        current.next = CircularNode(v)
        current = current.next
    current.next = head   # last node points back to head
    return head
```

Because there's no `null` to stop at, traversal code must track when it has returned to its starting node, or it will loop forever.

## 2.4 Arrays vs. Linked Lists

| Operation | Array | Linked List |
|---|---|---|
| Access by index | O(1) | O(n) |
| Search | O(n) | O(n) |
| Insert/delete at known position (with reference) | O(n) (shifting) | O(1) |
| Insert/delete at the front | O(n) | O(1) |
| Memory overhead | Low (just the values) | Higher (extra pointer per node) |
| Memory layout | Contiguous (cache-friendly) | Scattered (cache-unfriendly) |

Neither structure is "better" — they're optimized for opposite access patterns:

- Choose an **array** when you mostly read by position and rarely insert/delete in the middle.
- Choose a **linked list** when you're frequently inserting or deleting — especially at the front, or at a position you already have a pointer to — and you rarely need to jump straight to an arbitrary index.

Linked lists are also the foundation other structures build on: the stack and queue covered in the next two lessons can each be implemented directly on top of a linked list.

[Previous](./[1]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[3]-Stacks.md)
