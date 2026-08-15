[Previous](./[16]-Collections-Module.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[18]-Inheritance-and-Polymorphism.md)

*Object-Oriented Programming*

# Lesson 17 - Classes & Objects

## 17.1 What is a Class?

A **class** is a blueprint for creating objects — it defines what data (attributes) and behavior (methods) its instances will have. An **object** (or **instance**) is one concrete thing built from that blueprint.

```python
class Dog:
    pass

fido = Dog()   # fido is an instance (object) of the Dog class
rex = Dog()    # a separate, independent instance
```

`fido` and `rex` are both `Dog` objects, but they're distinct objects in memory.

---

## 17.2 Defining a Class and Creating Objects

Classes typically define attributes and methods together:

```python
class Dog:
    def bark(self):
        print("Woof!")

fido = Dog()
fido.bark()   # "Woof!"
```

Class names conventionally use `PascalCase` (e.g. `Dog`, `BankAccount`), distinguishing them from `snake_case` variables and functions.

---

## 17.3 The __init__ Method and self

`__init__` is a special method (see Lesson 4) automatically called when a new object is created, used to set up its initial state:

```python
class Dog:
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed

fido = Dog("Fido", "Labrador")
fido.name    # "Fido"
```

**`self`** refers to the specific instance the method is being called on. Every instance method must take `self` as its first parameter (Python passes it automatically) — it's how the method knows *which* object's data to work with.

---

## 17.4 Instance Attributes vs Class Attributes

- **Instance attributes** belong to one specific object, usually set in `__init__` via `self.x = ...`.
- **Class attributes** are shared by all instances of the class, defined directly in the class body.

```python
class Dog:
    species = "Canis familiaris"   # class attribute — shared by all dogs

    def __init__(self, name):
        self.name = name            # instance attribute — unique per dog

fido = Dog("Fido")
rex = Dog("Rex")

fido.species   # "Canis familiaris"
rex.species    # "Canis familiaris" — same value, shared
fido.name      # "Fido" — unique to fido
```

Be careful modifying a **mutable** class attribute (like a list) through an instance — since it's shared, changing it affects every instance.

---

## 17.5 Instance Methods

An instance method is a function defined inside a class that operates on a specific instance's data via `self`:

```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.balance = balance

    def deposit(self, amount):
        self.balance += amount

    def withdraw(self, amount):
        if amount > self.balance:
            raise ValueError("Insufficient funds")
        self.balance -= amount

account = BankAccount("Ada")
account.deposit(100)
account.withdraw(30)
account.balance   # 70
```

[Previous](./[16]-Collections-Module.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[18]-Inheritance-and-Polymorphism.md)
