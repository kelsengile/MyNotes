[Previous](./[20]-Magic-Methods.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[22]-Abstract-Base-Classes.md)

*Object-Oriented Programming*

# Lesson 21 - Class Methods, Static Methods & Properties

## 21.1 Instance Methods Recap

An ordinary method takes `self` as its first parameter and operates on one specific instance's data (see Lesson 1). Class methods and static methods, below, are two variations that don't work with a specific instance in that same way.

---

## 21.2 Class Methods (@classmethod)

A **class method** receives the class itself (conventionally named `cls`) instead of an instance, and is marked with the `@classmethod` decorator. It's commonly used for **alternative constructors** — extra ways to build an instance beyond the default `__init__`:

```python
class Pizza:
    def __init__(self, toppings):
        self.toppings = toppings

    @classmethod
    def margherita(cls):
        return cls(["mozzarella", "tomato"])   # cls() creates a new Pizza

    @classmethod
    def pepperoni(cls):
        return cls(["mozzarella", "pepperoni"])

pizza = Pizza.margherita()   # called on the class, not an instance
pizza.toppings   # ["mozzarella", "tomato"]
```

---

## 21.3 Static Methods (@staticmethod)

A **static method**, marked with `@staticmethod`, takes neither `self` nor `cls`. It behaves like a regular function that's simply grouped inside the class namespace because it's logically related:

```python
class MathUtils:
    @staticmethod
    def is_even(n):
        return n % 2 == 0

MathUtils.is_even(4)   # True — called without creating an instance
```

Use a static method when the logic doesn't need access to the instance or the class itself.

---

## 21.4 Properties (@property)

`@property` lets you define a method that's accessed like a plain attribute (no parentheses), commonly used to compute a value on the fly or to add validation to attribute access:

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14159 * self.radius ** 2

c = Circle(5)
c.area   # 78.53975 — called like an attribute, not c.area()
```

A matching `@<name>.setter` lets you intercept and validate assignment too:

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

    @radius.setter
    def radius(self, value):
        if value <= 0:
            raise ValueError("Radius must be positive")
        self._radius = value

c = Circle(5)
c.radius = 10    # runs the validation in the setter
c.radius = -1    # raises ValueError
```

This gives you attribute-style syntax with the validation power of a method — the best of both worlds, and a very idiomatic Python pattern.

[Previous](./[20]-Magic-Methods.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[22]-Abstract-Base-Classes.md)
