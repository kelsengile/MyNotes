[Previous](./[20]-Class-and-Static-Methods.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[22]-Dataclasses.md)

# esson 21 - Abstract Base Classes

---

## 21.1 What is an ABC?


An **Abstract Base Class (ABC)** defines a common interface that subclasses are *required* to implement, without providing a usable implementation itself. It formalizes the abstraction idea from Lesson 18.3: an ABC can't be instantiated directly, only used as a template other classes must complete.

---

## 21.2 The abc Module


Python provides the `abc` module for this purpose:

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

s = Shape()   # TypeError: Can't instantiate abstract class Shape with abstract method area
```

A class becomes abstract by inheriting from `ABC` and marking one or more methods with `@abstractmethod`.

---

## 21.3 Abstract Methods


Any subclass **must** override every abstract method, or it too remains uninstantiable:

```python
class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14159 * self.radius ** 2

c = Circle(5)     # works — area() is implemented
c.area()           # 78.53975

class Triangle(Shape):
    pass

t = Triangle()      # TypeError — area() was never implemented
```

This gives you a compile-time-like safety net (enforced at instantiation) ensuring every subclass honors the contract defined by the base class.

---

## 21.4 When to Use ABCs


Reach for an ABC when:
- You're designing a family of related classes (like different `Shape` types) that must all support the same operations.
- You want to guarantee, at the language level, that forgetting to implement a required method fails loudly and early rather than causing a confusing bug later.
- You're building a plugin-style system where third-party code should conform to a known interface.

For simpler cases where you just want default behavior a subclass *can* override but isn't required to, plain inheritance (Lesson 17) without `abc` is usually sufficient.

---

[Previous](./[20]-Class-and-Static-Methods.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[22]-Dataclasses.md)
