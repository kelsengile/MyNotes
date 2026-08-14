[Previous](./[17]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[19]-Magic-Methods.md)


# Lesson 18 - Encapsulation & Abstraction

---
## 18.1 Public, Protected, and Private Attributes



Python has no true "private" keyword — instead, it relies on naming conventions:

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance          # public — freely accessible
        self._pin = "1234"                # protected (convention) — "internal use"
        self.__account_number = "12345"    # private (name-mangled)

acc = BankAccount(100)
acc.balance        # fine
acc._pin            # works, but signals "please don't touch this from outside"
acc.__account_number   # AttributeError
acc._BankAccount__account_number   # works — Python "name-mangles" double underscores
```

A single leading underscore (`_pin`) is a convention meaning "internal, use with caution." A double leading underscore (`__account_number`) triggers **name mangling**, making accidental access from outside (or from subclasses) much less likely, though still not truly impossible.

---

## 18.2 Property-Based Access Control



The `@property` decorator lets you control attribute access with method-like logic, while keeping the simple `object.attribute` syntax for callers:

```python
class BankAccount:
    def __init__(self, balance):
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):
        if value < 0:
            raise ValueError("Balance cannot be negative")
        self._balance = value

acc = BankAccount(100)
acc.balance = 200     # calls the setter, validates the value
acc.balance = -50      # raises ValueError
```

This is the standard, Pythonic way to enforce invariants on an attribute without forcing callers to use explicit getter/setter method calls. See Lesson 20 for more on `@property`.

---

## 18.3 Abstraction as a Design Principle



Abstraction means exposing only what a user of a class needs to know, and hiding the internal implementation details. A well-designed class lets you call `account.withdraw(50)` without needing to know *how* the balance is stored or validated internally.

```python
class BankAccount:
    def __init__(self, balance):
        self._balance = balance

    def withdraw(self, amount):
        if amount > self._balance:
            raise ValueError("Insufficient funds")
        self._balance -= amount
        return self._balance
```

Combined with encapsulation, abstraction keeps a class's internals free to change (optimize, refactor, fix bugs) without breaking the code that uses it — as long as the public interface (its methods and their behavior) stays consistent. Abstract Base Classes, covered in Lesson 21, formalize this idea further.

---

[Previous](./[17]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[19]-Magic-Methods.md)
