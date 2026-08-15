[Previous](./[19]-Encapsulation-and-Abstraction.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[21]-Class-and-Static-Methods.md)

*Object-Oriented Programming*

# Lesson 20 - Magic/Dunder Methods

## 20.1 What Are Dunder/Magic Methods?

Methods surrounded by double underscores (like `__init__`) are called **dunder methods** ("double underscore") or **magic methods**. Python calls them automatically behind the scenes in response to built-in syntax and functions — they're how your custom classes hook into things like `print()`, `==`, `len()`, and arithmetic operators.

---

## 20.2 __str__ and __repr__

- **`__str__`** defines the readable string shown by `print()` and `str()` — meant for end users.
- **`__repr__`** defines the "official," unambiguous representation shown in the REPL and by `repr()` — meant for developers, ideally something that could recreate the object.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"({self.x}, {self.y})"

    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"

p = Point(3, 4)
print(p)     # (3, 4)          — uses __str__
p            # Point(x=3, y=4) — uses __repr__ in the REPL
```

If `__str__` isn't defined, Python falls back to `__repr__`. It's good practice to always define `__repr__`, even if `__str__` is skipped.

---

## 20.3 __eq__ and Other Comparisons

By default, `==` compares identity (like `is`) for custom objects. Define `__eq__` to compare by value instead:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

Point(1, 2) == Point(1, 2)   # True — now compares by value
```

Related methods: `__lt__` (`<`), `__le__` (`<=`), `__gt__` (`>`), `__ge__` (`>=`), `__ne__` (`!=`, usually derived automatically from `__eq__`).

---

## 20.4 __len__

Define `__len__` so `len()` works on your custom object:

```python
class Playlist:
    def __init__(self, songs):
        self.songs = songs

    def __len__(self):
        return len(self.songs)

playlist = Playlist(["Song A", "Song B", "Song C"])
len(playlist)   # 3
```

---

## 20.5 Other Common Magic Methods

```python
class Vector:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __add__(self, other):          # enables vector1 + vector2
        return Vector(self.x + other.x, self.y + other.y)

    def __getitem__(self, index):       # enables vector[0]
        return (self.x, self.y)[index]

    def __iter__(self):                 # enables `for value in vector`
        yield self.x
        yield self.y

    def __contains__(self, item):       # enables `value in vector`
        return item in (self.x, self.y)

v1, v2 = Vector(1, 2), Vector(3, 4)
v3 = v1 + v2         # __add__
v1[0]                 # __getitem__
list(v1)              # __iter__  → [1, 2]
2 in v1                # __contains__ → True
```

Other useful dunders include `__call__` (makes an object callable like a function), `__enter__`/`__exit__` (context managers, covered later in the course), and `__hash__` (makes an object usable as a dict key or set member).

[Previous](./[19]-Encapsulation-and-Abstraction.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[21]-Class-and-Static-Methods.md)
