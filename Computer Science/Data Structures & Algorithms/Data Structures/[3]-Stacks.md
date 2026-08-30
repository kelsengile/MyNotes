[Previous](./[2]-Linked-Lists.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[4]-Queues.md)

# Lesson 3 - Stacks

A stack is not a new way of storing data in memory — it's a restricted way of *accessing* data that happens to be stored in an array or a linked list. The restriction is what makes it useful: it forces a very specific, predictable order of operations that shows up constantly in real software.

## 3.1 The LIFO Principle

A stack follows **LIFO**: Last In, First Out. Picture a stack of plates — you can only add a plate to the top, and you can only remove the plate that's currently on top. You can't grab a plate from the middle without first removing everything above it.

A stack supports a small, fixed set of operations:

- **push(value)** — add an element to the top.
- **pop()** — remove and return the top element.
- **peek() / top()** — look at the top element without removing it.
- **isEmpty()** — check whether the stack has anything in it.

```python
stack = []
stack.append(1)   # push → [1]
stack.append(2)   # push → [1, 2]
stack.append(3)   # push → [1, 2, 3]
stack.pop()        # pop → returns 3, stack is [1, 2]
stack[-1]           # peek → 2
```

All four operations run in O(1), which is the entire appeal of a stack: it's a minimal interface that guarantees fast, predictable behavior by giving up flexibility.

## 3.2 Array-Based vs. Linked Implementations

A stack can be built on either of the structures from the last two lessons:

**Array-based stack** — push and pop both operate on the *end* of the array, which (as covered in Lesson 1) is the one position where array insert/delete is O(1) amortized. This is the most common real-world implementation, since it's cache-friendly and has low memory overhead.

```python
class ArrayStack:
    def __init__(self):
        self._data = []

    def push(self, value):
        self._data.append(value)          # O(1) amortized

    def pop(self):
        return self._data.pop()            # O(1)

    def peek(self):
        return self._data[-1]
```

**Linked-list-based stack** — push and pop both operate on the *head* of a singly linked list, which is also O(1) (as covered in Lesson 2), and avoids the occasional resize cost of a dynamic array at the expense of per-node pointer overhead.

```python
class LinkedStack:
    def __init__(self):
        self.head = None

    def push(self, value):
        node = Node(value)
        node.next = self.head
        self.head = node                    # O(1)

    def pop(self):
        value = self.head.value
        self.head = self.head.next
        return value                        # O(1)
```

Either implementation gives identical Big O behavior for all stack operations — the choice usually comes down to memory characteristics and language idioms rather than performance.

## 3.3 Common Use Cases

Stacks show up anywhere "undo the most recent thing" or "match the most recent unmatched thing" is the natural rule:

- **Function call management**: every time a function calls another function, the call stack pushes a new frame holding local variables and the return address; when the function returns, its frame is popped. Deep, unterminated recursion overflows this stack — the well-known "stack overflow" error.
- **Undo/redo features**: each action gets pushed onto a stack; undo pops the most recent one off.
- **Balanced bracket / parenthesis checking**: push every opening bracket you see; when you hit a closing bracket, pop and check it matches.

```python
def is_balanced(expression):
    pairs = {')': '(', ']': '[', '}': '{'}
    stack = []
    for char in expression:
        if char in '([{':
            stack.append(char)
        elif char in ')]}':
            if not stack or stack.pop() != pairs[char]:
                return False
    return len(stack) == 0

is_balanced("({[]})")   # True
is_balanced("({[}])")   # False — mismatched order
```

- **Browser back button / navigation history**: visiting a new page pushes it on; going back pops it off.
- **Depth-first search (DFS)**: whether implemented with explicit recursion (using the call stack) or an explicit stack, DFS always explores the most recently discovered node first — a direct application of LIFO.

[Previous](./[2]-Linked-Lists.md) | [Table of Contents](./[0]-Introduction-to-Data-Structures.md) | [Next](./[4]-Queues.md)
