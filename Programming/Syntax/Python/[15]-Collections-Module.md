[Previous](./[14]-Comprehensions.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[16]-OOP-Classes-and-Objects.md)

# Lesson 15 - Collections Module

---

## 15.1 Counter


`Counter` is a `dict` subclass for counting hashable items:

```python
from collections import Counter

words = ["apple", "banana", "apple", "cherry", "banana", "apple"]
counts = Counter(words)
# Counter({'apple': 3, 'banana': 2, 'cherry': 1})

counts.most_common(2)   # [('apple', 3), ('banana', 2)]
```

---

## 15.2 defaultdict


`defaultdict` automatically supplies a default value for missing keys, removing the need for manual existence checks:

```python
from collections import defaultdict

groups = defaultdict(list)
groups["fruits"].append("apple")   # no KeyError, even though "fruits" didn't exist yet
groups["fruits"].append("banana")
# defaultdict(<class 'list'>, {'fruits': ['apple', 'banana']})
```

The argument to `defaultdict` (here, `list`) is a factory function called to produce the default value whenever a missing key is accessed.

---

## 15.3 namedtuple


`namedtuple` creates tuple subclasses with named fields, giving you readable attribute access while keeping tuple immutability and performance:

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)
p.x, p.y   # 3, 4 — clearer than p[0], p[1]
```

For a more modern, feature-rich alternative, see dataclasses in Lesson 22.

---

## 15.4 deque and OrderedDict


```python
from collections import deque

d = deque([1, 2, 3])
d.appendleft(0)   # O(1) — fast at both ends, unlike list.insert(0, ...)
d.append(4)
d.popleft()        # removes and returns 0
```

`deque` (double-ended queue) is efficient for adding/removing from either end — useful for queues, sliding windows, and undo/redo stacks.

`OrderedDict` predates Python 3.7 and preserves insertion order, plus offers `move_to_end()` and equality that considers order. Since regular `dict` now preserves insertion order by default (Lesson 13.4), `OrderedDict` is mainly used when you specifically need its extra methods or order-sensitive equality.

---

[Previous](./[14]-Comprehensions.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[16]-OOP-Classes-and-Objects.md)
