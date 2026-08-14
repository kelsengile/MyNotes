[Previous](./[21]-Abstract-Base-Classes.md) | [Table of Contents](./[0]-Introduction-to-Python.md)


# Lesson 22 - Dataclasses

---
## 22.1 Why Dataclasses?


Many classes exist mainly to hold data, requiring boilerplate `__init__`, `__repr__`, and `__eq__` methods that all follow the same predictable pattern:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y
```

Dataclasses (Python 3.7+) generate all of this automatically from a simple class definition.

---

## 22.2 The @dataclass Decorator



```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

p1 = Point(3, 4)
p2 = Point(3, 4)
p1            # Point(x=3, y=4)   — __repr__ generated automatically
p1 == p2       # True               — __eq__ generated automatically
```

The type annotations (`x: int`, `y: int`) define the fields; `@dataclass` reads them and builds `__init__`, `__repr__`, and `__eq__` for you.

---

## 22.3 Default Values and Fields



```python
from dataclasses import dataclass, field

@dataclass
class Player:
    name: str
    score: int = 0                       # simple default value
    inventory: list = field(default_factory=list)   # mutable default — needs default_factory

p = Player("Ada")
p.score        # 0
p.inventory     # []
```

Mutable defaults (like `[]` or `{}`) can't be assigned directly as a default value — `field(default_factory=list)` tells the dataclass to call `list()` fresh for every new instance, avoiding the classic bug where all instances would otherwise share the same list.

---

## 22.4 Comparing Dataclasses to Regular Classes and NamedTuples



| | Regular class | `namedtuple` (Lesson 15.3) | `@dataclass` |
|---|---|---|---|
| Mutable | Yes | No | Yes (by default) |
| Auto `__init__`/`__repr__`/`__eq__` | No, write by hand | Yes | Yes |
| Supports methods, inheritance | Yes | Limited | Yes, fully |
| Type hints | Optional | No | Required for fields |

Use a `@dataclass` when you want a class that's primarily a structured data container but may still need methods, mutability, or inheritance — it removes boilerplate while keeping full class capabilities. Use `namedtuple` when you specifically want immutability and tuple behavior; write a regular class when the object's behavior matters more than its data.

This closes out the Object-Oriented Programming section of this course — Lesson 23 onward moves into more advanced language features like generators and decorators.

---

[Previous](./[21]-Abstract-Base-Classes.md) | [Table of Contents](./[0]-Introduction-to-Python.md)
