[Previous](./[17]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[19]-Encapsulation-and-Abstraction.md)

*Object-Oriented Programming*

# Lesson 18 - Inheritance & Polymorphism

## 18.1 What is Inheritance?

**Inheritance** lets a class (the **subclass** / **child class**) reuse and extend the attributes and methods of another class (the **superclass** / **parent class**), modeling an "is-a" relationship:

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        print(f"{self.name} makes a sound.")

class Dog(Animal):   # Dog inherits from Animal
    pass

fido = Dog("Fido")
fido.speak()   # "Fido makes a sound." — inherited from Animal
```

---

## 18.2 Overriding Methods

A subclass can **override** a parent method by redefining it with the same name, replacing the inherited behavior:

```python
class Dog(Animal):
    def speak(self):
        print(f"{self.name} barks.")

fido = Dog("Fido")
fido.speak()   # "Fido barks." — overridden version is used
```

---

## 18.3 The super() Function

`super()` gives access to the parent class's methods from within a subclass — commonly used to extend (rather than fully replace) the parent's behavior, especially in `__init__`:

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)   # call Animal's __init__ to set self.name
        self.breed = breed

fido = Dog("Fido", "Labrador")
fido.name    # "Fido"
fido.breed   # "Labrador"
```

---

## 18.4 Multiple Inheritance

Python allows a class to inherit from more than one parent class:

```python
class Swimmer:
    def swim(self):
        print("Swimming")

class Runner:
    def run(self):
        print("Running")

class Triathlete(Swimmer, Runner):
    pass

athlete = Triathlete()
athlete.swim()   # "Swimming"
athlete.run()     # "Running"
```

Multiple inheritance is powerful but can get confusing if parent classes define methods with the same name — which is where MRO (next) matters.

---

## 18.5 Method Resolution Order (MRO)

When a class has multiple parents (possibly with overlapping method names), Python needs a defined order to search for a method — this is the **Method Resolution Order**, computed with the C3 linearization algorithm. You can inspect it directly:

```python
class A:
    def greet(self):
        print("A")

class B(A):
    def greet(self):
        print("B")

class C(A):
    def greet(self):
        print("C")

class D(B, C):
    pass

D().greet()          # "B" — found on B first, per MRO
print(D.__mro__)     # (D, B, C, A, object) — the search order
```

Python searches left to right through the parents listed, then up the inheritance chain — `D` checks itself, then `B`, then `C`, then `A`, then the base `object` class.

---

## 18.6 Polymorphism

**Polymorphism** ("many forms") means objects of different classes can be used interchangeably as long as they share a common interface — the same method call produces type-appropriate behavior:

```python
class Cat(Animal):
    def speak(self):
        print(f"{self.name} meows.")

animals = [Dog("Fido"), Cat("Whiskers")]

for animal in animals:
    animal.speak()   # each calls its OWN version of speak()
# "Fido barks."
# "Whiskers meows."
```

This is the foundation of writing flexible code — the calling code (the `for` loop above) doesn't need to know or care which exact subclass it's working with.

[Previous](./[17]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[19]-Encapsulation-and-Abstraction.md)
