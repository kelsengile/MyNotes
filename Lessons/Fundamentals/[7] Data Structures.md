# Data Structures

Data structures are ways of organizing and storing data so it can be accessed and modified efficiently. Choosing the right structure affects the performance and clarity of a program — different structures trade off speed of lookup, insertion, deletion, and memory usage differently.

---

## 7.1 Arrays / Lists

Arrays (and lists) store an ordered collection of elements, typically accessed by index.

### 7.1.1 Fixed-Size Arrays

In lower-level or statically typed languages, arrays often have a fixed size set at creation and store elements of a single type contiguously in memory.

```c
// C
int numbers[5] = {1, 2, 3, 4, 5};
printf("%d\n", numbers[2]); // 3
```

```java
// Java
int[] numbers = {1, 2, 3, 4, 5};
System.out.println(numbers[2]); // 3
```

### 7.1.2 Dynamic Arrays / Lists

Many languages provide resizable list types that can grow or shrink at runtime.

```python
# Python
numbers = [1, 2, 3]
numbers.append(4)        # [1, 2, 3, 4]
numbers.insert(0, 0)     # [0, 1, 2, 3, 4]
numbers.remove(2)        # [0, 1, 3, 4]
print(numbers[1])        # 1
print(numbers[-1])       # 4 (negative indexing)
print(numbers[1:3])      # [1, 3] (slicing)
```

```javascript
// JavaScript
let numbers = [1, 2, 3];
numbers.push(4);         // [1, 2, 3, 4]
numbers.unshift(0);      // [0, 1, 2, 3, 4]
numbers.splice(2, 1);    // remove one element at index 2
console.log(numbers[1]);
```

### 7.1.3 Common Operations & Complexity

| Operation                | Typical Time Complexity |
|---------------------------|--------------------------|
| Access by index            | O(1)                    |
| Search (unsorted)          | O(n)                    |
| Insert/remove at end       | O(1) amortized          |
| Insert/remove at beginning/middle | O(n)             |

### 7.1.4 Multi-Dimensional Arrays

Arrays of arrays represent grids, matrices, or tables.

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
]
print(matrix[1][2])  # 6
```

**Key points:**
- Arrays/lists preserve insertion order.
- Best suited when frequent access by position/index is needed.
- Insertions/deletions in the middle are costly since elements must shift.

---

## 7.2 Strings

Strings represent sequences of characters. In many languages they behave like arrays of characters but with additional built-in text-processing behavior.

```python
s = "Hello, World!"
print(s.lower())          # hello, world!
print(s.upper())          # HELLO, WORLD!
print(s.split(", "))      # ['Hello', 'World!']
print(s.replace("World", "Python"))  # Hello, Python!
print(s[0:5])              # Hello (slicing)
print(len(s))               # 13
```

```javascript
let s = "Hello, World!";
console.log(s.toLowerCase());
console.log(s.split(", "));
console.log(s.replace("World", "JS"));
console.log(s.slice(0, 5));
console.log(s.length);
```

### 7.2.1 Mutability

- In Python, JavaScript, and Java, strings are **immutable** — operations like `replace` or concatenation return a *new* string rather than modifying the original.
- In C, strings are typically mutable char arrays (`char[]`), and care must be taken with buffer sizes and null terminators (`'\0'`).
- In some languages (e.g., Rust, C++'s `std::string`), a distinction exists between immutable string slices/views and mutable owned string buffers.

### 7.2.2 Common String Operations

| Operation                  | Example                          |
|------------------------------|-----------------------------------|
| Concatenation                 | `"a" + "b"` → `"ab"`             |
| Length                          | `len(s)` / `s.length`           |
| Substring/slicing               | `s[1:4]` / `s.substring(1, 4)`  |
| Search                          | `s.find("x")` / `s.indexOf("x")`|
| Split into a list               | `s.split(",")`                  |
| Join a list into a string       | `",".join(list)`                |
| Trim whitespace                 | `s.strip()` / `s.trim()`        |

### 7.2.3 String Building

Repeated concatenation in a loop can be inefficient (O(n²) in some languages) because each concatenation may create a new string. Prefer a mutable buffer or built-in join method for large-scale building:

```python
# Efficient: build a list, then join once
parts = []
for i in range(1000):
    parts.append(str(i))
result = ",".join(parts)
```

```java
// Java: StringBuilder avoids repeated string allocation
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
String result = sb.toString();
```

---

## 7.3 Dictionaries / Maps / Hash Tables

A dictionary (also called a map, associative array, or hash table) stores **key-value pairs**, allowing fast lookup of a value given its key.

```python
person = {"name": "Alice", "age": 30}
print(person["name"])       # Alice
person["email"] = "a@x.com" # add new key
del person["age"]           # remove a key
print("name" in person)     # True
for key, value in person.items():
    print(key, value)
```

```javascript
// JavaScript (Object or Map)
const person = { name: "Alice", age: 30 };
person.email = "a@x.com";
delete person.age;
console.log("name" in person);

// Map is often preferred for dynamic key sets
const map = new Map();
map.set("name", "Alice");
map.get("name");
map.has("name");
```

```java
// Java
Map<String, Integer> ages = new HashMap<>();
ages.put("Alice", 30);
ages.get("Alice");
ages.containsKey("Alice");
```

### 7.3.1 How Hash Tables Work

Internally, a hash table applies a **hash function** to each key to compute an index into an underlying array (bucket). This gives average O(1) lookup, insertion, and deletion. When two keys hash to the same bucket (a **collision**), strategies like chaining (storing a list per bucket) or open addressing (probing for the next free slot) resolve the conflict.

### 7.3.2 Complexity

| Operation      | Average  | Worst Case (many collisions) |
|-----------------|----------|-------------------------------|
| Lookup            | O(1)     | O(n)                          |
| Insert             | O(1)     | O(n)                          |
| Delete              | O(1)    | O(n)                          |

**Key points:**
- Keys are typically required to be unique and hashable/immutable (e.g., strings, numbers, tuples in Python — not lists).
- Order of keys is insertion order in many modern implementations (e.g., Python 3.7+ dicts, JavaScript objects for string keys), but this should not always be relied upon across languages.

---

## 7.4 Sets

A set stores an **unordered collection of unique values** — duplicates are automatically discarded.

```python
s = {1, 2, 3}
s.add(4)
s.add(2)          # no effect, 2 already present
s.remove(1)
print(3 in s)      # True

a = {1, 2, 3}
b = {2, 3, 4}
print(a | b)   # union: {1, 2, 3, 4}
print(a & b)   # intersection: {2, 3}
print(a - b)   # difference: {1}
```

```javascript
const s = new Set([1, 2, 3]);
s.add(4);
s.add(2);      // no effect
s.delete(1);
console.log(s.has(3));   // true
```

**Common uses:**
- Removing duplicates from a collection.
- Fast membership testing (`x in set`) — typically O(1) average, backed by a hash table internally.
- Set algebra: union, intersection, difference, symmetric difference.

---

## 7.5 Stacks and Queues

Stacks and queues are ordered collections that restrict how elements are added and removed.

### 7.5.1 Stacks (LIFO — Last In, First Out)

The most recently added element is the first one removed. Common operations: `push` (add) and `pop` (remove from the top).

```python
stack = []
stack.append(1)  # push
stack.append(2)
stack.append(3)
print(stack.pop())  # 3 (LIFO)
```

```javascript
const stack = [];
stack.push(1);
stack.push(2);
console.log(stack.pop()); // 2
```

**Use cases:** undo/redo functionality, function call stacks, expression evaluation (e.g., matching parentheses), depth-first traversal.

### 7.5.2 Queues (FIFO — First In, First Out)

The first element added is the first one removed. Common operations: `enqueue` (add to back) and `dequeue` (remove from front).

```python
from collections import deque
queue = deque()
queue.append(1)   # enqueue
queue.append(2)
queue.append(3)
print(queue.popleft())  # 1 (FIFO)
```

```javascript
// JavaScript arrays can act as queues, though shift() is O(n)
const queue = [];
queue.push(1);
queue.push(2);
console.log(queue.shift()); // 1
```

> Note: Using a plain array/list as a queue can be inefficient because removing from the front typically requires shifting all remaining elements (O(n)). A `deque` (double-ended queue) or linked list gives O(1) operations at both ends.

**Use cases:** task scheduling, breadth-first traversal, buffering/streaming data, print job queues.

### 7.5.3 Deques (Double-Ended Queues)

A deque supports efficient insertion and removal from **both** ends, generalizing stacks and queues into one structure.

```python
from collections import deque
dq = deque()
dq.append(1)       # add to right
dq.appendleft(0)   # add to left
dq.pop()           # remove from right
dq.popleft()       # remove from left
```

---

## 7.6 Linked Lists

A linked list stores elements as a sequence of **nodes**, where each node holds a value and a reference (pointer) to the next node (and, in a doubly linked list, the previous node too). Unlike arrays, elements are not stored contiguously in memory.

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None

    def append(self, value):
        new_node = Node(value)
        if not self.head:
            self.head = new_node
            return
        current = self.head
        while current.next:
            current = current.next
        current.next = new_node

    def to_list(self):
        result = []
        current = self.head
        while current:
            result.append(current.value)
            current = current.next
        return result

ll = LinkedList()
ll.append(1)
ll.append(2)
ll.append(3)
print(ll.to_list())  # [1, 2, 3]
```

### 7.6.1 Singly vs. Doubly Linked Lists

- **Singly linked list:** each node points only to the next node. Traversal is one-directional.
- **Doubly linked list:** each node points to both the next and previous nodes, allowing traversal in both directions at the cost of extra memory per node.

### 7.6.2 Arrays vs. Linked Lists

| Aspect                  | Array / List         | Linked List           |
|--------------------------|-----------------------|-------------------------|
| Access by index            | O(1)                | O(n)                   |
| Insert/delete at known position (with reference) | O(n) (shifting) | O(1)      |
| Memory layout               | Contiguous          | Scattered (pointers)   |
| Memory overhead              | Low                | Higher (stores pointers)|
| Cache performance             | Better (locality)  | Worse                  |

**Use cases:** implementing stacks/queues, scenarios with frequent insertions/deletions at arbitrary positions, and as a building block for more advanced structures.

---

## 7.7 Trees and Graphs

### 7.7.1 Trees

A tree is a hierarchical structure made of nodes, where each node has a value and references to **child** nodes, starting from a single **root** node. Each node (except the root) has exactly one parent, and there are no cycles.

**Key terminology:**
- **Root** — the topmost node.
- **Leaf** — a node with no children.
- **Height/Depth** — the length of the path from root to a given node (or the longest path to a leaf).

#### Binary Trees

Each node has at most two children, commonly referred to as `left` and `right`.

```python
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

root = TreeNode(10)
root.left = TreeNode(5)
root.right = TreeNode(15)
```

#### Binary Search Trees (BST)

A binary tree where, for every node, all values in the left subtree are smaller and all values in the right subtree are larger, enabling efficient searching.

```python
def insert(node, value):
    if node is None:
        return TreeNode(value)
    if value < node.value:
        node.left = insert(node.left, value)
    else:
        node.right = insert(node.right, value)
    return node
```

| Operation      | Balanced BST (avg) | Unbalanced BST (worst) |
|-----------------|----------------------|---------------------------|
| Search             | O(log n)           | O(n)                     |
| Insert              | O(log n)          | O(n)                     |
| Delete               | O(log n)         | O(n)                     |

#### Tree Traversal

- **Depth-first traversals:** *pre-order* (node, left, right), *in-order* (left, node, right — yields sorted order for a BST), *post-order* (left, right, node).
- **Breadth-first traversal (level-order):** visit nodes level by level, typically using a queue.

```python
def in_order(node, result):
    if node:
        in_order(node.left, result)
        result.append(node.value)
        in_order(node.right, result)
    return result
```

**Other common tree types:** balanced trees (AVL, Red-Black) that self-adjust to guarantee O(log n) operations; heaps (used to implement priority queues); tries (specialized for prefix-based string search).

### 7.7.2 Graphs

A graph is a more general structure consisting of **nodes (vertices)** connected by **edges**, which may or may not have direction or weight. Unlike trees, graphs can contain cycles and nodes can have multiple connections in any pattern.

**Key terminology:**
- **Directed graph** — edges have a direction (A → B does not imply B → A).
- **Undirected graph** — edges are bidirectional.
- **Weighted graph** — edges carry a value (e.g., distance, cost).
- **Cycle** — a path that starts and ends at the same node.

#### Representations

**Adjacency list** (common, space-efficient for sparse graphs):

```python
graph = {
    "A": ["B", "C"],
    "B": ["A", "D"],
    "C": ["A"],
    "D": ["B"],
}
```

**Adjacency matrix** (fast edge lookup, better for dense graphs):

```python
#      A  B  C  D
matrix = [
    [0, 1, 1, 0],  # A
    [1, 0, 0, 1],  # B
    [1, 0, 0, 0],  # C
    [0, 1, 0, 0],  # D
]
```

#### Graph Traversal

- **Depth-first search (DFS):** explores as far as possible along each branch before backtracking, typically implemented with recursion or an explicit stack.
- **Breadth-first search (BFS):** explores all neighbors at the current depth before moving deeper, implemented with a queue. Useful for finding the shortest path in an unweighted graph.

```python
def bfs(graph, start):
    visited = {start}
    queue = [start]
    order = []
    while queue:
        node = queue.pop(0)
        order.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    return order
```

**Use cases:** social networks, maps/navigation (shortest path algorithms like Dijkstra's), dependency resolution, recommendation systems, network routing.

### 7.7.3 Trees vs. Graphs

| Aspect          | Tree                        | Graph                          |
|-------------------|--------------------------------|-----------------------------------|
| Cycles              | Not allowed                  | Allowed                          |
| Root                 | Exactly one                 | No inherent root                 |
| Parent-child             | Each node has ≤1 parent  | No parent/child restriction      |
| Special case              | A tree is a type of graph (connected, acyclic) | — |

---

## Choosing the Right Structure

| Need                                              | Good Fit                          |
|-----------------------------------------------------|--------------------------------------|
| Ordered data, frequent index access                    | Array / List                       |
| Fast key-based lookup                                  | Dictionary / Map / Hash Table      |
| Uniqueness / membership testing                         | Set                               |
| LIFO processing (undo, backtracking)                     | Stack                             |
| FIFO processing (scheduling, BFS)                          | Queue                             |
| Frequent insert/delete at arbitrary positions                | Linked List                     |
| Hierarchical data (file systems, DOM, org charts)                | Tree                          |
| Networked / interconnected data (routes, relationships)             | Graph                     |