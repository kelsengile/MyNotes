[Previous](./[15]-Comprehensions.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[17]-OOP-Classes-and-Objects.md)

*Data Structures*

# Lesson 16 - Collections Module

## 16.1 Counter

`Counter` is a `dict` subclass for counting hashable items — perfect for tallying occurrences:

```python
from collections import Counter

words = ["apple", "banana", "apple", "cherry", "apple"]
counts = Counter(words)
# Counter({'apple': 3, 'banana': 1, 'cherry': 1})

counts.most_common(2)   # [('apple', 3), ('banana', 1)] — top 2 most common
counts["apple"]          # 3
counts["missing"]        # 0 — no KeyError, unlike a normal dict
```

---

## 16.2 defaultdict

A `defaultdict` behaves like a regular dictionary, but automatically creates a default value for any missing key instead of raising a `KeyError` — useful for grouping data:

```python
from collections import defaultdict

groups = defaultdict(list)
groups["fruits"].append("apple")   # no need to check if "fruits" exists first
groups["fruits"].append("banana")
groups["veggies"].append("carrot")
# {"fruits": ["apple", "banana"], "veggies": ["carrot"]}
```

The argument to `defaultdict` (here, `list`) is a factory function called to produce the default value — common choices are `list`, `int`, or `set`.

---

## 16.3 namedtuple

`namedtuple` creates a lightweight, immutable class-like tuple where fields can be accessed by name instead of only by index, improving readability:

```python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)

p.x       # 3
p.y       # 4
p[0]      # 3 — still works like a regular tuple too
```

For most modern code, the `dataclasses` module (covered later in this course) has largely replaced `namedtuple` for this purpose, but it's still common in existing codebases.

---

## 16.4 deque

A `deque` ("deck", double-ended queue) is a list-like container optimized for fast additions and removals from **both ends** — something a regular list is slow at:

```python
from collections import deque

d = deque([1, 2, 3])
d.append(4)         # add to the right end
d.appendleft(0)      # add to the left end
d.pop()               # remove from the right end
d.popleft()           # remove from the left end
# d is now deque([1, 2, 3])
```

`deque` is the right tool whenever you need a queue, a stack, or a sliding "last N items" buffer (via `deque(maxlen=N)`).

---

## 16.5 OrderedDict

`OrderedDict` is a dictionary subclass that explicitly remembers the order items were inserted, and additionally supports `.move_to_end()`:

```python
from collections import OrderedDict

od = OrderedDict()
od["b"] = 2
od["a"] = 1
list(od.items())   # [('b', 2), ('a', 1)] — insertion order preserved

od.move_to_end("b")  # moves "b" to the end
```

Since Python 3.7, regular `dict` also preserves insertion order, so `OrderedDict` is mainly useful today when you specifically need `.move_to_end()`, or need to compare two dicts by order (`OrderedDict` equality is order-sensitive, unlike a plain `dict`).

[Previous](./[15]-Comprehensions.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[17]-OOP-Classes-and-Objects.md)
