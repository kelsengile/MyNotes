[Previous](./[18]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[20]-Magic-Methods.md)

*Object-Oriented Programming*

# Lesson 19 - Encapsulation & Abstraction

## 19.1 What is Encapsulation?

**Encapsulation** is the practice of bundling data and the methods that operate on it together inside a class, while restricting direct outside access to some of that data — protecting an object's internal state from being changed in invalid ways.

```python
class BankAccount:
    def __init__(self, balance):
        self._balance = balance   # not meant to be accessed directly from outside

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit must be positive")
        self._balance += amount
```

By funneling changes through methods like `deposit()`, the class can enforce rules (like "no negative deposits") that direct attribute access would bypass.

---

## 19.2 Public, Protected, and Private Attributes

Python doesn't have true access-restriction keywords like `private` in Java — it relies on **naming conventions** instead:

| Prefix | Convention | Meaning |
|---|---|---|
| `name` | Public | Freely accessible from anywhere |
| `_name` | Protected | "Internal use" by convention only — still accessible, but signals "don't touch this from outside" |
| `__name` | Private | Triggers name mangling (see 3.3) to discourage accidental access |

```python
class Example:
    def __init__(self):
        self.public = 1
        self._protected = 2
        self.__private = 3
```

Nothing actually *prevents* access to `_protected` — it's a convention that other developers (and you) are expected to respect.

---

## 19.3 Name Mangling

Attributes prefixed with a double underscore (and no more than one trailing underscore) get automatically renamed internally to `_ClassName__attribute`, making them harder (though not impossible) to access accidentally from outside the class:

```python
class Example:
    def __init__(self):
        self.__secret = 42

e = Example()
e.__secret            # AttributeError — doesn't exist under this name
e._Example__secret     # 42 — the actual mangled name still works
```

This is mainly designed to prevent naming collisions in subclasses, not to provide real security.

---

## 19.4 Abstraction

**Abstraction** means exposing only the relevant, high-level details of an object while hiding the complex implementation underneath. A well-designed class lets you call `account.withdraw(50)` without needing to know how the balance is stored or validated internally.

```python
class Car:
    def start_engine(self):
        self.__check_fuel()
        self.__ignite()
        print("Engine started")

    def __check_fuel(self):
        pass  # internal detail, hidden from the user

    def __ignite(self):
        pass  # internal detail, hidden from the user

car = Car()
car.start_engine()   # simple public interface — internals are hidden
```

Abstraction and encapsulation work together: encapsulation hides *data*, abstraction hides *complexity*, and both are expressed through a class's public interface. Python's `abc` module (Lesson 6) formalizes abstraction further with abstract base classes.

[Previous](./[18]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[20]-Magic-Methods.md)
