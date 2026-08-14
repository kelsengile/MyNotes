[Previous](./[19]-Magic-Methods.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[21]-Abstract-Base-Classes.md)


# Lesson 20 - Class Methods, Static Methods & Properties

---
## 20.1 Instance Methods Recap



As covered in Lesson 16, a regular instance method automatically receives the calling instance as its first argument, `self`, giving it access to that instance's data:

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14159 * self.radius ** 2
```

---

## 20.2 classmethod



A `classmethod` receives the **class itself** (conventionally named `cls`) rather than an instance, and is marked with `@classmethod`. It's commonly used for alternative constructors:

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @classmethod
    def from_diameter(cls, diameter):
        return cls(diameter / 2)   # cls(...) calls Circle(...)

c = Circle.from_diameter(10)
c.radius   # 5.0
```

`from_diameter` gives users a second, clearly-named way to build a `Circle`, without cluttering `__init__` with multiple, less-obvious parameter combinations.

---

## 20.3 staticmethod


A `staticmethod`, marked with `@staticmethod`, receives neither `self` nor `cls` — it's just a regular function that happens to live inside the class's namespace because it's logically related:

```python
class Circle:
    @staticmethod
    def is_valid_radius(value):
        return value > 0

Circle.is_valid_radius(5)    # True — called on the class, no instance needed
```

Use `staticmethod` for utility functions that belong conceptually with a class but don't need access to instance or class data.

---

## 20.4 property



As introduced in Lesson 18.2, `@property` turns a method into something accessed like a plain attribute, letting you compute a value on demand:

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14159 * self.radius ** 2

c = Circle(5)
c.area   # 78.53975 — called like an attribute, no parentheses, but computed each time
```

This is preferable to storing `area` as a plain attribute that could silently go stale if `radius` changes later — with `@property`, it's always recalculated from the current `radius`.

---

[Previous](./[19]-Magic-Methods.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[21]-Abstract-Base-Classes.md)
