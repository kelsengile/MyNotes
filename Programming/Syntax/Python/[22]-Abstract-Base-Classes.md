[Previous](./[21]-Class-and-Static-Methods.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[23]-Dataclasses.md)

*Object-Oriented Programming*

# Lesson 22 - Abstract Base Classes (ABC module)

## 22.1 What is an Abstract Base Class?

An **abstract base class (ABC)** defines a common interface that subclasses are required to implement, but which can never be instantiated on its own. It's a way of saying "every subclass of this must provide these methods," enforced by Python itself rather than just by convention or documentation.

---

## 22.2 The abc Module

Python's built-in `abc` module provides the `ABC` base class and the `@abstractmethod` decorator used to build abstract base classes:

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass
```

---

## 22.3 Defining Abstract Methods

Any method decorated with `@abstractmethod` **must** be overridden by a concrete (non-abstract) subclass — Python raises an error at instantiation time if one is missing:

```python
shape = Shape()   # TypeError: Can't instantiate abstract class Shape
                   # with abstract methods area, perimeter

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

    def perimeter(self):
        return 2 * (self.width + self.height)

rect = Rectangle(4, 5)   # works — all abstract methods implemented
rect.area()                # 20
```

If `Rectangle` had forgotten to implement `perimeter()`, instantiating it would raise the same `TypeError`.

---

## 22.4 Why Use ABCs?

- **Enforce a contract** — guarantees every subclass provides the methods that calling code depends on, catching missing implementations immediately rather than at some later runtime call.
- **Communicate intent** — clearly documents "this class defines an interface; don't instantiate it directly, subclass it."
- **Enable polymorphism safely** — code that works with a `Shape` can call `.area()` on any subclass with confidence, since the ABC guarantees the method exists.

ABCs are especially valuable in larger codebases and libraries where multiple developers implement different subclasses of a shared interface.

[Previous](./[21]-Class-and-Static-Methods.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[23]-Dataclasses.md)
