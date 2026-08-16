[Previous](./[20]-Static-and-Final.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[22]-Enums.md)

*Object-Oriented Programming*

# Lesson 21 - Nested, Inner, Local & Anonymous Classes

Java lets you define a class inside another class or even inside a method. This lesson covers the four flavors of this and when each is useful.

## 21.1 Static Nested Classes

A **static nested class** is declared `static` inside another class. It behaves like a regular top-level class, just namespaced inside its enclosing class, and does **not** have access to the outer class's instance members:

```java
public class Outer {
    static class Nested {
        void greet() {
            System.out.println("Hello from a static nested class!");
        }
    }
}

Outer.Nested nested = new Outer.Nested();
nested.greet();
```

This is useful for grouping a helper class tightly with the class that uses it, such as `Map.Entry` in the standard library.

---

## 21.2 Inner (Non-Static) Classes

An **inner class** (no `static` keyword) is tied to a specific instance of its outer class, and can directly access that instance's fields and methods:

```java
public class Outer {
    int value = 42;

    class Inner {
        void show() {
            System.out.println("Outer's value is: " + value);
        }
    }
}

Outer outer = new Outer();
Outer.Inner inner = outer.new Inner(); // requires an outer instance
inner.show();
```

Creating an inner class instance always requires an existing outer instance, since it needs that context to access outer fields.

---

## 21.3 Local Classes

A **local class** is defined entirely inside a method body, and is only visible within that method:

```java
public void processOrder() {
    class Validator {
        boolean isValid(int amount) {
            return amount > 0;
        }
    }

    Validator v = new Validator();
    System.out.println(v.isValid(50));
}
```

Local classes are useful when a helper class is only ever needed within one method, and defining it as a full top-level class would be overkill.

---

## 21.4 Anonymous Classes

An **anonymous class** lets you define and instantiate a one-off subclass or interface implementation in a single expression, with no name:

```java
public interface Greeter {
    void greet();
}

Greeter g = new Greeter() {
    @Override
    public void greet() {
        System.out.println("Hi there!");
    }
};
g.greet();
```

This is commonly used for quick, throwaway implementations — though in modern Java, **lambda expressions** (covered in [Lesson 34](./[34]-Lambda-Expressions.md)) have replaced anonymous classes for simple single-method interfaces.

---

## 21.5 When to Use Each

| Type | Access outer instance? | Best for |
|---|---|---|
| Static nested | No | Logical grouping, no outer-instance dependency |
| Inner (non-static) | Yes | Tightly coupled helper needing outer state |
| Local | Yes (final/effectively-final locals) | One-off helper used only within a method |
| Anonymous | Yes | Quick, single-use implementation of an interface/class |

As a general rule, prefer the simplest option that fits: a static nested class if you don't need outer-instance access, and reach for local/anonymous classes only for small, throwaway logic.

---

[Previous](./[20]-Static-and-Final.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[22]-Enums.md)