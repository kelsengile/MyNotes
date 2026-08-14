[Previous](./[15]-Collections-Module.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[17]-Inheritance-and-Polymorphism.md)


Lesson 16 - Classes & Objects

--- 

## 16.1 Defining a Class



A class is a blueprint for creating objects that bundle data (attributes) and behavior (methods) together:

```python
class Dog:
    pass

my_dog = Dog()   # creating an "instance" of the class
```

By convention, class names use `PascalCase` while everything else in Python (variables, functions) uses `snake_case`.

---

## 16.2 The __init__ Method and self



`__init__` is a special method (see Lesson 19) automatically called when a new instance is created, used to set up initial attributes:

```python
class Dog:
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed

my_dog = Dog("Rex", "Labrador")
my_dog.name    # "Rex"
```

`self` refers to the specific instance being created or acted upon, and is always the first parameter of an instance method — Python passes it automatically when you call `my_dog.some_method()`.

---

## 16.3 Instance vs Class Attributes



```python
class Dog:
    species = "Canis familiaris"   # class attribute — shared by ALL instances

    def __init__(self, name):
        self.name = name             # instance attribute — unique per instance

a = Dog("Rex")
b = Dog("Fido")
a.name, b.name         # "Rex", "Fido" — different per instance
a.species, b.species    # both "Canis familiaris" — shared
```

Be careful modifying a **mutable** class attribute (like a list) — because it's shared, changing it through one instance affects every instance.

---

## 16.4 Methods



Methods are functions defined inside a class that operate on an instance's data:

```python
class Dog:
    def __init__(self, name):
        self.name = name

    def bark(self):
        return f"{self.name} says Woof!"

my_dog = Dog("Rex")
my_dog.bark()   # "Rex says Woof!"
```

Calling `my_dog.bark()` is equivalent to `Dog.bark(my_dog)` — Python automatically passes the instance as `self`. Class methods, static methods, and properties (variations on this pattern) are covered in Lesson 20.

---

[Previous](./[15]-Collections-Module.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[17]-Inheritance-and-Polymorphism.md)
