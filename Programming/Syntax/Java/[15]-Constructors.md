[Previous](./[14]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[16]-Inheritance-and-Polymorphism.md)

*Object-Oriented Programming*

# Lesson 15 - Constructors & the `this` Keyword

Manually assigning every field after creating an object, as we did last lesson, is tedious and error-prone. This lesson introduces constructors — the proper way to initialize objects.

## 15.1 Default Constructors

If you don't define any constructor, Java automatically provides a **default constructor** that takes no arguments and leaves fields at their default values:

```java
public class Dog {
    String name;
    int age;
}

Dog d = new Dog(); // uses the invisible default constructor
```

As soon as you define any constructor of your own, this automatic default constructor disappears.

---

## 15.2 Parameterized Constructors

A **constructor** is a special method, matching the class name, with no return type, that runs when an object is created — typically used to require and assign initial values:

```java
public class Dog {
    String name;
    int age;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

Dog rex = new Dog("Rex", 3);
```

Now every `Dog` is guaranteed to start with a name and age, rather than relying on manual assignment afterward.

---

## 15.3 Constructor Overloading

Like regular methods, constructors can be **overloaded** — a class can offer multiple constructors with different parameter lists, giving callers flexibility in how they create objects:

```java
public class Dog {
    String name;
    int age;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Dog(String name) {
        this.name = name;
        this.age = 0; // default age
    }
}
```

---

## 15.4 The this Keyword

`this` refers to the current object instance. It's most commonly used inside a constructor or method to distinguish an instance field from a parameter that shares the same name:

```java
public Dog(String name, int age) {
    this.name = name; // this.name = the field; name = the parameter
    this.age = age;
}
```

Without `this`, `name = name;` would just assign the parameter to itself and leave the field untouched.

---

## 15.5 Constructor Chaining (this(...))

A constructor can call another constructor in the *same* class using `this(...)`, avoiding duplicated initialization logic. This must be the very first statement in the constructor:

```java
public class Dog {
    String name;
    int age;

    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Dog(String name) {
        this(name, 0); // delegates to the two-argument constructor
    }
}
```

This keeps your initialization logic in one place, rather than repeating it across every overload.

---

[Previous](./[14]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[16]-Inheritance-and-Polymorphism.md)