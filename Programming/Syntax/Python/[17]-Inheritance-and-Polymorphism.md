[Previous](./[16]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[18]-Encapsulation-and-Abstraction.md)


# Lesson 17 - Inheritance & Polymorphism

---
## 17.1 Basic Inheritance



A class can inherit attributes and methods from another class, letting you reuse and extend existing behavior:

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return f"{self.name} makes a sound"

class Dog(Animal):        # Dog inherits from Animal
    pass

d = Dog("Rex")
d.speak()   # "Rex makes a sound" — inherited from Animal
```

`Animal` is the **parent** (or base/super) class; `Dog` is the **child** (or derived/sub) class.

---

## 17.2 Overriding Methods and super()



A child class can redefine (override) a method it inherits, and use `super()` to still call the parent's version:

```python
class Dog(Animal):
    def speak(self):
        original = super().speak()
        return f"{original}... specifically, a bark!"

    def __init__(self, name, breed):
        super().__init__(name)   # let the parent handle its own setup
        self.breed = breed
```

`super()` avoids hardcoding the parent class name, which keeps code correct even if the inheritance chain changes later.

---

## 17.3 Multiple Inheritance and MRO



Python allows a class to inherit from more than one parent:

```python
class Swimmer:
    def move(self):
        return "swimming"

class Runner:
    def move(self):
        return "running"

class Triathlete(Swimmer, Runner):
    pass

Triathlete().move()   # "swimming" — Swimmer is checked first
```

When multiple parents define the same method, Python resolves which one wins using the **Method Resolution Order (MRO)** — roughly, parents are checked left to right as listed in the class definition. You can inspect it directly:

```python
Triathlete.__mro__
```



## 17.4 Polymorphism

---

Polymorphism means objects of different classes can be used interchangeably if they share a common interface — the same method call produces behavior appropriate to each object's actual type:

```python
class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow"

animals = [Dog("Rex", "Lab"), Cat("Whiskers")]
for animal in animals:
    print(animal.speak())   # each calls its own version of speak()
```

This is the foundation of writing flexible code that works with a family of related classes without needing to know each one's exact type.

---

[Previous](./[16]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[18]-Encapsulation-and-Abstraction.md)
