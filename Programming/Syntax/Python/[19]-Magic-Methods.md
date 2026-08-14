[Previous](./[18]-Encapsulation-and-Abstraction.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[20]-Class-and-Static-Methods.md)

# Lesson 19 - Magic/Dunder Methods

---

## 19.1 __str__ and __repr__



"Magic methods" (or "dunder" methods, for **d**ouble **under**score) let your objects hook into Python's built-in syntax and functions.

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __str__(self):
        return f"({self.x}, {self.y})"        # for humans — used by print(), str()

    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"  # for developers — used in the REPL, debugging

p = Point(3, 4)
print(p)     # (3, 4)               — calls __str__
p            # Point(x=3, y=4)      — calls __repr__ in the REPL
```

If `__str__` is missing, Python falls back to `__repr__`. A good `__repr__` should ideally be unambiguous enough that it could recreate the object.

---

## 19.2 __eq__ and Comparison Methods


By default, two instances are only equal if they're the *same object* in memory. `__eq__` lets you define equality based on values instead:

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

Point(1, 2) == Point(1, 2)   # True, now that __eq__ is defined
```

Related methods include `__lt__`, `__le__`, `__gt__`, `__ge__` for `<`, `<=`, `>`, `>=` — defining these lets instances of your class be sorted with `sorted()`.

---

## 19.3 __len__, __getitem__, and Container Methods


Implementing these lets your custom objects behave like built-in containers:

```python
class Playlist:
    def __init__(self, songs):
        self.songs = songs

    def __len__(self):
        return len(self.songs)

    def __getitem__(self, index):
        return self.songs[index]

pl = Playlist(["Song A", "Song B", "Song C"])
len(pl)      # 3           — calls __len__
pl[0]         # "Song A"    — calls __getitem__
for song in pl:   # __getitem__ also makes the object iterable
    print(song)
```

---

## 19.4 Operator Overloading


Arithmetic and other operators are also backed by dunder methods, letting you define what `+`, `-`, `*`, etc. mean for your own classes:

```python
class Vector:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)

    def __repr__(self):
        return f"Vector({self.x}, {self.y})"

Vector(1, 2) + Vector(3, 4)   # Vector(4, 6) — calls __add__
```

Other common ones: `__sub__` (`-`), `__mul__` (`*`), `__truediv__` (`/`), `__contains__` (`in`). This is how, at a fundamental level, built-in types like `int` and `str` implement their own operator behavior too.

---

[Previous](./[18]-Encapsulation-and-Abstraction.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[20]-Class-and-Static-Methods.md)
