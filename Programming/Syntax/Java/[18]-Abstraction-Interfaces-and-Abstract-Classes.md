[Previous](./[17]-Encapsulation-and-Access-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[19]-The-Object-Class.md)

*Object-Oriented Programming*

# Lesson 18 - Abstraction: Abstract Classes & Interfaces

Abstraction lets you define *what* something should do without necessarily specifying *how*, allowing different classes to fulfil the same contract in their own way. This lesson covers Java's two main abstraction tools.

## 18.1 What Is Abstraction?

**Abstraction** means focusing on essential behavior while hiding implementation detail. In Java, this is achieved primarily through **abstract classes** and **interfaces** — both let you define a contract that other classes must fulfil, without dictating every detail upfront.

---

## 18.2 Abstract Classes

An **abstract class** cannot be instantiated directly — it exists to be extended. It can mix fully-implemented methods with **abstract methods** (no body), which subclasses are required to implement:

```java
public abstract class Shape {
    abstract double area(); // no body — subclasses must implement this

    void describe() {       // regular method, shared by all subclasses
        System.out.println("This shape has an area of " + area());
    }
}

public class Circle extends Shape {
    double radius;

    Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * radius * radius;
    }
}
```

`new Shape()` would be a compile error — only concrete subclasses like `Circle` can be instantiated.

---

## 18.3 Interfaces

An **interface** defines a contract of method signatures that implementing classes must fulfil, using `implements` rather than `extends`. Unlike classes, a class can implement **multiple** interfaces:

```java
public interface Movable {
    void move();
}

public interface Soundable {
    void makeSound();
}

public class Dog implements Movable, Soundable {
    public void move() {
        System.out.println("Running!");
    }

    public void makeSound() {
        System.out.println("Woof!");
    }
}
```

This is Java's answer to the fact that a class can only `extend` one superclass, but real-world objects often fit multiple independent contracts.

---

## 18.4 Default and Static Interface Methods

Modern Java interfaces can include **default methods** (with a body, using the `default` keyword) that implementing classes inherit automatically, and **static methods** that belong to the interface itself:

```java
public interface Greeter {
    void greet(String name);

    default void greetLoudly(String name) {
        greet(name.toUpperCase() + "!!!");
    }

    static Greeter simple() {
        return name -> System.out.println("Hi, " + name);
    }
}
```

Default methods let an interface evolve over time — adding a new default method doesn't break classes that already implement it.

---

## 18.5 Abstract Class vs Interface

| | Abstract Class | Interface |
|---|---|---|
| Instantiable? | No | No |
| Can have fields with state? | Yes | Only `static final` constants |
| Multiple inheritance? | No (single `extends`) | Yes (multiple `implements`) |
| Constructors? | Yes | No |
| Use when... | Classes share common state/implementation | Unrelated classes share a capability/contract |

A common rule of thumb: use an interface to describe *what* something can do; use an abstract class when you also want to share common state or implementation across closely related subclasses.

---

[Previous](./[17]-Encapsulation-and-Access-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[19]-The-Object-Class.md)