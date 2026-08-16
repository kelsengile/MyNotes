[Previous](./[13]-Exception-Handling.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[15]-Constructors.md)

*Object-Oriented Programming*

# Lesson 14 - Classes & Objects

Java is a fundamentally object-oriented language. This lesson introduces the two core building blocks of that paradigm: classes and objects.

## 14.1 What Is a Class?

A **class** is a blueprint that defines the structure and behavior shared by a group of objects. It describes what data an object will hold (fields) and what it can do (methods), but a class by itself doesn't represent a specific "thing" — it's a template.

```java
public class Dog {
    String name;
    int age;

    void bark() {
        System.out.println(name + " says Woof!");
    }
}
```

---

## 14.2 Fields and Methods

**Fields** (also called instance variables) hold an object's data, and **methods** define its behavior. Together, they represent the state and capabilities of every object built from that class:

```java
public class Dog {
    String name;   // field
    int age;       // field

    void bark() {           // method
        System.out.println(name + " says Woof!");
    }

    boolean isPuppy() {     // method
        return age < 1;
    }
}
```

---

## 14.3 Creating Objects

An **object** is a concrete instance of a class, created with the `new` keyword. Each object has its own independent copy of the class's fields:

```java
Dog myDog = new Dog();
myDog.name = "Rex";
myDog.age = 3;
myDog.bark(); // "Rex says Woof!"

Dog anotherDog = new Dog();
anotherDog.name = "Bella";
// myDog and anotherDog are completely separate objects
```

You can create as many objects from a single class as you need, each with its own independent state.

---

## 14.4 Instance vs Class Members

The fields and methods we've seen so far (`name`, `bark()`) are **instance members** — they belong to a specific object, and each object gets its own copy. Java also supports **static (class) members**, which belong to the class itself and are shared across every instance. We'll cover this distinction fully in [Lesson 20](./[20]-Static-and-Final.md); for now, know that plain fields and methods without the `static` keyword are tied to individual objects.

---

## 14.5 Object References

As covered in [Lesson 5](./[5]-Variables-and-Data-Types.md), an object variable doesn't store the object directly — it stores a **reference** to it. This means two variables can point to the *same* object:

```java
Dog myDog = new Dog();
myDog.name = "Rex";

Dog sameDog = myDog; // sameDog points to the SAME object
sameDog.name = "Max";

System.out.println(myDog.name); // prints "Max" — they're the same object!
```

Understanding that assignment copies the *reference*, not the object, is essential for reasoning correctly about object behavior in Java.

---

[Previous](./[13]-Exception-Handling.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[15]-Constructors.md)