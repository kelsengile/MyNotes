[Previous](./[15]-Constructors.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[17]-Encapsulation-and-Access-Modifiers.md)

*Object-Oriented Programming*

# Lesson 16 - Inheritance & Polymorphism (`extends`, method overriding)

Inheritance lets classes share and extend behavior, avoiding duplicated code across related types. This lesson covers inheritance and the polymorphism it enables.

## 16.1 The extends Keyword

A class can **inherit** from another class using `extends`, automatically gaining its fields and methods. The class being inherited from is the **superclass** (or parent); the inheriting class is the **subclass** (or child):

```java
public class Animal {
    String name;

    void eat() {
        System.out.println(name + " is eating.");
    }
}

public class Dog extends Animal {
    void bark() {
        System.out.println(name + " says Woof!");
    }
}

Dog rex = new Dog();
rex.name = "Rex";
rex.eat();  // inherited from Animal
rex.bark(); // defined in Dog
```

---

## 16.2 Method Overriding

A subclass can **override** a method it inherits, providing its own implementation instead of the parent's. Mark overrides with `@Override` — the compiler will then catch mistakes if the signature doesn't actually match a parent method:

```java
public class Animal {
    void makeSound() {
        System.out.println("Some generic animal sound");
    }
}

public class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Woof!");
    }
}
```

Overriding is different from overloading (Lesson 10) — overriding replaces an inherited method's behavior in a subclass; overloading defines multiple methods with the same name but different parameters in the same class.

---

## 16.3 super Keyword

`super` refers to the parent class, and is used to call an overridden method's original implementation, or to invoke the parent's constructor:

```java
public class Dog extends Animal {
    @Override
    void makeSound() {
        super.makeSound(); // still runs Animal's version first
        System.out.println("...and also Woof!");
    }

    public Dog(String name) {
        super(); // calls Animal's constructor
        this.name = name;
    }
}
```

If a subclass constructor doesn't explicitly call `super(...)`, Java inserts a call to the parent's no-argument constructor automatically as the first statement.

---

## 16.4 Polymorphism

**Polymorphism** ("many forms") means a variable of a superclass type can refer to an object of any subclass, and calling a method on it runs the *actual* object's version — not the declared type's version:

```java
Animal myPet = new Dog(); // declared as Animal, but IS a Dog
myPet.makeSound();        // runs Dog's makeSound(), not Animal's
```

This is enormously powerful: you can write code that operates on the general `Animal` type, and it automatically works correctly for any subclass, without needing to know which specific one it is at compile time.

---

## 16.5 The Object Superclass

Every class you write in Java — even if you don't say `extends` anything — implicitly inherits from a single root class called `Object`. This is why every object automatically has methods like `toString()` and `equals()` available. We'll explore `Object` and these inherited methods in depth in [Lesson 19](./[19]-The-Object-Class.md).

---

[Previous](./[15]-Constructors.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[17]-Encapsulation-and-Access-Modifiers.md)