[Previous](./[22]-Abstract-Base-Classes.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[24]-Pydantic.md)

*Object-Oriented Programming*

# Lesson 23 - Dataclasses

## 23.1 The Problem Dataclasses Solve

Plain classes that primarily just hold data require a lot of repetitive boilerplate — a manual `__init__`, plus manually written `__repr__` and `__eq__` if you want readable printing and value comparison:

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

For a simple data-holding class, that's a lot of code to write and maintain by hand.

---

## 23.2 The @dataclass Decorator

The built-in `dataclasses` module generates all of that boilerplate automatically from type-annotated class attributes:

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

p1 = Point(3, 4)
p2 = Point(3, 4)

print(p1)          # Point(x=3, y=4) — __repr__ generated automatically
p1 == p2            # True — __eq__ generated automatically
```

`@dataclass` automatically generates `__init__`, `__repr__`, and `__eq__` based on the annotated fields, with far less code than the manual version above.

---

## 23.3 Default Values and Field()

Fields can have default values, just like function parameters:

```python
@dataclass
class Point:
    x: int = 0
    y: int = 0

Point()          # Point(x=0, y=0)
Point(x=5)        # Point(x=5, y=0)
```

Mutable defaults (like a list) can't be assigned directly — use `field(default_factory=...)` instead, which avoids the classic "shared mutable default" bug:

```python
from dataclasses import dataclass, field

@dataclass
class Team:
    name: str
    members: list = field(default_factory=list)   # a NEW list per instance

team_a = Team("Alpha")
team_b = Team("Beta")
team_a.members.append("Ada")
team_b.members   # [] — unaffected, each instance got its own list
```

---

## 23.4 Comparing and Ordering Dataclasses

`@dataclass` generates `__eq__` by default (comparing all fields). Passing `order=True` also generates `__lt__`, `__le__`, `__gt__`, and `__ge__`, comparing fields in the order they're declared — useful for sorting:

```python
@dataclass(order=True)
class Version:
    major: int
    minor: int

Version(1, 5) < Version(2, 0)   # True
sorted([Version(2, 0), Version(1, 5)])  # [Version(1, 5), Version(2, 0)]
```

---

## 23.5 Immutable Dataclasses (frozen=True)

Passing `frozen=True` makes instances immutable after creation — attempting to reassign a field raises an error, similar to a tuple:

```python
@dataclass(frozen=True)
class Point:
    x: int
    y: int

p = Point(3, 4)
p.x = 10   # FrozenInstanceError: cannot assign to field 'x'
```

Frozen dataclasses are also hashable by default (assuming all their fields are hashable), so they can be used as dictionary keys or set members — something a regular mutable dataclass cannot do.

[Previous](./[22]-Abstract-Base-Classes.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[24]-Pydantic.md)
