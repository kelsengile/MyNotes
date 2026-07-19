# Object-Oriented Programming

Object-oriented programming (OOP) is a paradigm that organizes code around **objects** — bundles of data (state) and behavior (methods) — rather than around functions and logic alone. It aims to model real-world entities and their interactions, and to make large codebases more modular, reusable, and maintainable.

---

## 10.1 Classes and Objects

### 10.1.1 Classes

A **class** is a blueprint that defines the structure (fields/attributes) and behavior (methods) that its objects will have. It doesn't itself hold data — it describes what data and behavior any object built from it will have.

```python
# Python
class Dog:
    def __init__(self, name, breed):  # constructor
        self.name = name
        self.breed = breed

    def bark(self):
        print(f"{self.name} says Woof!")
```

```java
// Java
class Dog {
    String name;
    String breed;

    Dog(String name, String breed) {  // constructor
        this.name = name;
        this.breed = breed;
    }

    void bark() {
        System.out.println(name + " says Woof!");
    }
}
```

```javascript
// JavaScript
class Dog {
    constructor(name, breed) {
        this.name = name;
        this.breed = breed;
    }

    bark() {
        console.log(`${this.name} says Woof!`);
    }
}
```

### 10.1.2 Objects (Instances)

An **object** (or **instance**) is a concrete realization of a class — created via **instantiation** — with its own actual values for the fields the class defines.

```python
my_dog = Dog("Rex", "Labrador")   # instantiate the class
your_dog = Dog("Bella", "Poodle")

my_dog.bark()    # Rex says Woof!
your_dog.bark()  # Bella says Woof!
```

Each instance has its own independent copy of the instance data (`name`, `breed`), even though they share the same method definitions from the class.

### 10.1.3 Constructors and Destructors

A **constructor** is a special method that runs automatically when a new object is created, typically used to initialize its fields.

```python
class Point:
    def __init__(self, x=0, y=0):  # __init__ is Python's constructor
        self.x = x
        self.y = y
```

Some languages also support a **destructor**, run when an object is destroyed/deallocated, often used to release resources (file handles, network connections).

```cpp
// C++
class FileHandler {
public:
    FileHandler() { /* open file */ }
    ~FileHandler() { /* close file automatically */ }  // destructor
};
```

### 10.1.4 Fields/Attributes and Methods

- **Fields (attributes/properties):** the data an object holds.
- **Methods:** functions defined on a class that operate on that object's data, usually accessed via an implicit reference to the current instance (`self` in Python, `this` in Java/JavaScript/C++).

### 10.1.5 Class (Static) Members

Some fields/methods belong to the **class itself** rather than to any one instance, and are shared across all instances.

```python
class Dog:
    species = "Canis familiaris"  # class attribute — shared by all instances

    def __init__(self, name):
        self.name = name          # instance attribute — unique per object

    @classmethod
    def get_species(cls):
        return cls.species

print(Dog.species)          # accessed on the class directly
print(Dog("Rex").species)   # also accessible via an instance
```

---

## 10.2 Encapsulation

**Encapsulation** is the bundling of data and the methods that operate on it within a single unit (the object), combined with restricting direct access to some of that data from outside — exposing only what's necessary through a controlled interface.

### 10.2.1 Access Modifiers

Most OOP languages provide keywords to control visibility of fields and methods:

| Modifier      | Meaning                                              |
|-----------------|-----------------------------------------------------|
| `public`          | Accessible from anywhere                            |
| `private`           | Accessible only within the defining class          |
| `protected`           | Accessible within the class and its subclasses    |

```java
// Java
class BankAccount {
    private double balance;   // hidden from outside access

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    public double getBalance() {   // controlled read access
        return balance;
    }

    public void deposit(double amount) {  // controlled write access, with validation
        if (amount > 0) {
            balance += amount;
        }
    }
}
```

### 10.2.2 Encapsulation in Python

Python doesn't enforce strict access control, relying instead on naming conventions:
- `_name` — a single underscore signals "internal use" (a convention, not enforced).
- `__name` — a double underscore triggers *name mangling*, making accidental access from outside harder (though still not truly private).

```python
class BankAccount:
    def __init__(self, balance):
        self._balance = balance   # convention: "protected", please don't touch directly

    def get_balance(self):
        return self._balance

    def deposit(self, amount):
        if amount > 0:
            self._balance += amount
```

### 10.2.3 Why Encapsulation Matters

- **Protects invariants:** internal validation logic (e.g., "balance can't go negative") is guaranteed to run because outside code can't bypass it by mutating the field directly.
- **Reduces coupling:** external code depends only on a class's public interface, not its internal implementation — the internals can change freely without breaking other code.
- **Improves maintainability:** bugs related to invalid state are easier to trace since all mutations go through a small set of controlled methods.

### 10.2.4 Getters and Setters

Methods that provide controlled read/write access to private fields, sometimes with added validation or computed logic.

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):          # getter
        return self._radius

    @radius.setter
    def radius(self, value):    # setter, with validation
        if value < 0:
            raise ValueError("Radius cannot be negative")
        self._radius = value

c = Circle(5)
c.radius = 10      # calls the setter
print(c.radius)     # calls the getter — 10
```

---

## 10.3 Inheritance

**Inheritance** lets one class (the **subclass** / **derived class**) acquire the fields and methods of another class (the **superclass** / **base class**), enabling code reuse and the modeling of "is-a" relationships.

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        print(f"{self.name} makes a sound.")

class Dog(Animal):          # Dog inherits from Animal
    def speak(self):          # method overriding
        print(f"{self.name} barks.")

class Cat(Animal):
    def speak(self):
        print(f"{self.name} meows.")

Dog("Rex").speak()    # Rex barks.
Cat("Tom").speak()    # Tom meows.
Animal("Generic").speak()  # Generic makes a sound.
```

```java
// Java
class Animal {
    String name;
    Animal(String name) { this.name = name; }
    void speak() { System.out.println(name + " makes a sound."); }
}

class Dog extends Animal {
    Dog(String name) { super(name); }   // call the parent constructor
    @Override
    void speak() { System.out.println(name + " barks."); }
}
```

### 10.3.1 Method Overriding

A subclass can **override** a method it inherits, providing its own implementation while keeping the same method signature (as `speak()` does above).

### 10.3.2 Calling the Parent Class

Subclasses often need to call the parent's version of a method or constructor, typically via `super`.

```python
class Dog(Animal):
    def speak(self):
        super().speak()          # call the parent's implementation first
        print(f"{self.name} also barks.")
```

### 10.3.3 Single vs. Multiple Inheritance

- **Single inheritance:** a class inherits from exactly one parent (Java, C#, JavaScript).
- **Multiple inheritance:** a class can inherit from more than one parent class at once (C++, Python).

```python
class Flyer:
    def fly(self):
        print("Flying")

class Swimmer:
    def swim(self):
        print("Swimming")

class Duck(Flyer, Swimmer):   # multiple inheritance
    pass

Duck().fly()
Duck().swim()
```

Multiple inheritance introduces complexity — for example, the **diamond problem**, where two parent classes define a method with the same name and it's ambiguous which one a subclass should inherit. Languages resolve this differently (Python uses a defined Method Resolution Order; Java and C# avoid it entirely by disallowing multiple class inheritance, offering **interfaces** instead).

### 10.3.4 Inheritance Hierarchies

Inheritance can span multiple levels (`GoldenRetriever` → `Dog` → `Animal`), and a subclass inherits (transitively) everything from all of its ancestors.

---

## 10.4 Polymorphism

**Polymorphism** ("many forms") is the ability for different types to be treated through a common interface, with each type responding to the same call in its own way.

### 10.4.1 Runtime (Subtype) Polymorphism

The most common form in OOP: code written against a base type/interface works correctly with any subclass, and the correct overridden method is chosen automatically at runtime based on the object's actual type.

```python
animals = [Dog("Rex"), Cat("Tom"), Animal("Generic")]
for animal in animals:
    animal.speak()   # each calls its own version of speak(), automatically
```

```java
// Java
Animal[] animals = { new Dog("Rex"), new Cat("Tom") };
for (Animal a : animals) {
    a.speak();   // calls Dog.speak() or Cat.speak() depending on actual object type
}
```

This is powerful because calling code doesn't need to know or check the concrete type of each object — it just relies on the shared interface (`speak()`), and the correct behavior is dispatched automatically.

### 10.4.2 Method Overloading (Compile-Time / Ad-Hoc Polymorphism)

Some languages allow multiple methods with the *same name* but different parameter lists (types/counts) within the same class — the correct one is chosen based on the arguments provided.

```java
// Java
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
    int add(int a, int b, int c) { return a + b + c; }
}
```

> Note: Python and JavaScript don't support true method overloading — a later definition of the same method name simply replaces earlier ones. Similar effects are achieved via default arguments or checking argument types/counts manually.

### 10.4.3 Interfaces and Polymorphism

An **interface** defines a contract (a set of method signatures) that implementing classes must fulfill, without specifying how. Any class implementing an interface can be used wherever that interface is expected, regardless of its concrete type.

```java
// Java
interface Shape {
    double area();
}

class Circle implements Shape {
    double radius;
    Circle(double radius) { this.radius = radius; }
    public double area() { return Math.PI * radius * radius; }
}

class Rectangle implements Shape {
    double width, height;
    Rectangle(double w, double h) { width = w; height = h; }
    public double area() { return width * height; }
}

Shape[] shapes = { new Circle(3), new Rectangle(4, 5) };
for (Shape s : shapes) {
    System.out.println(s.area());   // works uniformly regardless of concrete type
}
```

### 10.4.4 Duck Typing

In dynamically typed languages like Python and JavaScript, polymorphism is often achieved informally: "if it walks like a duck and quacks like a duck, it's a duck." Any object with the expected method can be used, without needing to formally implement an interface or share a common base class.

```python
class Duck:
    def make_sound(self):
        print("Quack")

class Robot:
    def make_sound(self):
        print("Beep")

for thing in [Duck(), Robot()]:
    thing.make_sound()   # works for both — no shared base class required
```

---

## 10.5 Abstraction

**Abstraction** means exposing only the essential features of an object/system while hiding unnecessary implementation detail, letting users of a class interact with *what* it does without needing to know *how* it does it.

### 10.5.1 Abstract Classes

An **abstract class** cannot be instantiated directly — it exists to be subclassed, and typically defines some methods that subclasses *must* implement, alongside potentially some shared, already-implemented behavior.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):     # subclasses must implement this
        pass

    def describe(self):  # concrete, shared method
        print(f"This shape has an area of {self.area()}")

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    def area(self):
        return 3.14159 * self.radius ** 2

# Shape()          # TypeError — cannot instantiate an abstract class
Circle(5).describe()  # This shape has an area of 78.53975
```

```java
// Java
abstract class Shape {
    abstract double area();          // must be implemented by subclasses
    void describe() {                  // shared, concrete method
        System.out.println("Area: " + area());
    }
}
```

### 10.5.2 Interfaces as Pure Abstraction

An interface takes abstraction further — it defines *only* the contract (method signatures), with no implementation at all (in most languages, though some allow default method bodies), leaving 100% of the "how" to implementing classes.

### 10.5.3 Abstraction vs. Encapsulation

These two concepts are related but distinct:

| Concept          | Focus                                          | Answers the question       |
|--------------------|-------------------------------------------------|-------------------------------|
| Abstraction           | Hiding complexity by exposing a simplified interface | "What does this do?"    |
| Encapsulation             | Restricting access to internal data/state    | "How is access controlled?" |

A well-designed class is usually both: it presents a simple, abstract interface to its users (abstraction) while protecting its internal fields from uncontrolled external modification (encapsulation).

### 10.5.4 Why Abstraction Matters

- Reduces cognitive load — callers only need to understand a class's public interface, not its internals.
- Allows implementation details to change freely (e.g., swapping a sorting algorithm, changing internal storage) without affecting code that depends on the abstraction.
- Encourages designing around behavior/contracts rather than concrete implementations, which supports polymorphism and testability (e.g., substituting a mock implementation of an interface in tests).

---

## 10.6 Composition over Inheritance

**Composition** builds complex objects by combining ("has-a") simpler objects, rather than by inheriting ("is-a") from a base class. This is a common design principle: favor assembling behavior from independent, focused components instead of building deep inheritance hierarchies.

### 10.6.1 The Problem with Deep Inheritance

Inheritance hierarchies can become rigid and fragile as they grow:
- A subclass inherits *everything* from its parent, even parts it doesn't need.
- Changes to a base class can unexpectedly ripple through every subclass (the **fragile base class** problem).
- Modeling combinations of behavior through inheritance alone can lead to awkward or exploding class hierarchies (e.g., `FlyingSwimmingDog`, `FlyingNonSwimmingDog`, ...).

```python
# Inheritance-heavy approach — becomes unwieldy as combinations grow
class Bird:
    def fly(self):
        print("Flying")

class Penguin(Bird):   # awkward: penguins can't actually fly
    def fly(self):
        raise Exception("Penguins can't fly!")
```

### 10.6.2 Composition Example

Instead, behaviors can be modeled as separate, composable objects that a class holds a reference to and delegates to, rather than inherits from.

```python
class FlyingBehavior:
    def move(self):
        print("Flying through the air")

class SwimmingBehavior:
    def move(self):
        print("Swimming through water")

class WalkingBehavior:
    def move(self):
        print("Walking on land")

class Bird:
    def __init__(self, movement_behavior):
        self.movement_behavior = movement_behavior  # "has-a" relationship

    def move(self):
        self.movement_behavior.move()

eagle = Bird(FlyingBehavior())
penguin = Bird(WalkingBehavior())
eagle.move()      # Flying through the air
penguin.move()    # Walking on land
```

Behaviors can now be mixed, swapped, or extended independently, without touching a shared inheritance tree — and a bird's movement behavior could even be changed dynamically at runtime.

### 10.6.3 When to Use Each

| Use Inheritance when...                              | Use Composition when...                                     |
|-----------------------------------------------------------|------------------------------------------------------------------|
| There's a genuine, stable "is-a" relationship                 | The relationship is more "has-a" or "can-do"                  |
| Subclasses truly share behavior without meaningful exceptions   | Behavior needs to be mixed, swapped, or reused across unrelated classes |
| The hierarchy is shallow and unlikely to grow awkwardly            | You want to avoid rigid, deep hierarchies and increase flexibility |

This is often summarized as the **"composition over inheritance"** principle — not a rule to *never* use inheritance, but a reminder to default to composition when it isn't clearly the better fit, since it tends to produce more flexible and maintainable designs.

---

## 10.7 SOLID Principles

SOLID is a set of five design principles for writing maintainable, flexible object-oriented code, popularized by Robert C. Martin.

### 10.7.1 S — Single Responsibility Principle (SRP)

*A class should have only one reason to change* — i.e., it should be responsible for a single, well-defined piece of functionality.

```python
# Violates SRP — this class both manages employee data AND handles reporting/persistence
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def calculate_pay(self):
        return self.salary

    def save_to_database(self):   # unrelated responsibility
        ...

    def generate_report(self):    # another unrelated responsibility
        ...
```

```python
# Follows SRP — responsibilities are split into focused classes
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def calculate_pay(self):
        return self.salary

class EmployeeRepository:
    def save(self, employee):
        ...

class EmployeeReportGenerator:
    def generate(self, employee):
        ...
```

### 10.7.2 O — Open/Closed Principle (OCP)

*Classes should be open for extension but closed for modification* — new behavior should be added by writing new code (e.g., new subclasses/implementations), not by editing and risking existing, tested code.

```python
# Violates OCP — adding a new shape requires modifying this function's internals
def calculate_area(shape):
    if shape.type == "circle":
        return 3.14159 * shape.radius ** 2
    elif shape.type == "rectangle":
        return shape.width * shape.height
    # every new shape requires another elif branch here

# Follows OCP — new shapes can be added by creating new classes,
# without touching existing code
class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    def area(self):
        return 3.14159 * self.radius ** 2

class Triangle(Shape):   # extension — no existing code modified
    def __init__(self, base, height):
        self.base, self.height = base, height
    def area(self):
        return 0.5 * self.base * self.height
```

### 10.7.3 L — Liskov Substitution Principle (LSP)

*Subtypes must be substitutable for their base types* without breaking the correctness of the program — i.e., anywhere a base class is expected, a subclass should work correctly too, honoring the same expected behavior/contract.

```python
# Violates LSP — Square changes the expected behavior of Rectangle's interface
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def set_width(self, w):
        self.width = w

    def set_height(self, h):
        self.height = h

class Square(Rectangle):
    def set_width(self, w):        # overridden in a way that breaks expectations
        self.width = w
        self.height = w             # unexpected side effect not implied by Rectangle

def resize(rect):
    rect.set_width(5)
    rect.set_height(10)
    assert rect.width * rect.height == 50   # fails for a Square!
```

This is the classic "square-rectangle problem" — it illustrates that inheritance should reflect genuine behavioral compatibility, not just a superficial conceptual relationship.

### 10.7.4 I — Interface Segregation Principle (ISP)

*Clients should not be forced to depend on interfaces/methods they don't use.* Prefer several small, specific interfaces over one large, general-purpose one.

```python
# Violates ISP — forces all workers to implement irrelevant methods
class Worker(ABC):
    @abstractmethod
    def work(self): pass
    @abstractmethod
    def eat(self): pass

class RobotWorker(Worker):
    def work(self): print("Working")
    def eat(self): raise NotImplementedError("Robots don't eat!")  # forced, unused method

# Follows ISP — split into focused, specific interfaces
class Workable(ABC):
    @abstractmethod
    def work(self): pass

class Eatable(ABC):
    @abstractmethod
    def eat(self): pass

class HumanWorker(Workable, Eatable):
    def work(self): print("Working")
    def eat(self): print("Eating lunch")

class RobotWorker(Workable):   # only implements what's relevant
    def work(self): print("Working")
```

### 10.7.5 D — Dependency Inversion Principle (DIP)

*High-level modules should not depend on low-level modules; both should depend on abstractions* (interfaces), rather than high-level logic being tightly coupled to specific concrete implementations.

```python
# Violates DIP — NotificationService is tightly coupled to a specific concrete class
class EmailSender:
    def send(self, message):
        print(f"Emailing: {message}")

class NotificationService:
    def __init__(self):
        self.sender = EmailSender()   # hardcoded dependency on a concrete class

    def notify(self, message):
        self.sender.send(message)

# Follows DIP — depends on an abstraction, not a concrete implementation
class MessageSender(ABC):
    @abstractmethod
    def send(self, message): pass

class EmailSender(MessageSender):
    def send(self, message):
        print(f"Emailing: {message}")

class SMSSender(MessageSender):
    def send(self, message):
        print(f"Texting: {message}")

class NotificationService:
    def __init__(self, sender: MessageSender):   # injected dependency, any implementation works
        self.sender = sender

    def notify(self, message):
        self.sender.send(message)

# Easy to swap implementations without changing NotificationService at all
NotificationService(EmailSender()).notify("Hello!")
NotificationService(SMSSender()).notify("Hello!")
```

This last example also demonstrates **dependency injection** — a common technique for satisfying DIP by passing dependencies into a class (e.g., via its constructor) rather than having it construct them internally.

### 10.7.6 Why SOLID Matters

Together, these principles aim to produce code that is easier to extend with new features, easier to test in isolation (since dependencies can be swapped for mocks/stubs), and more resilient to the ripple effects of change — with each class having a clear, focused purpose and interacting with others through stable abstractions rather than tightly coupled implementation details.