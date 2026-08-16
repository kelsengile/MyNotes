[Previous](./[19]-The-Object-Class.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[21]-Nested-and-Inner-Classes.md)

*Object-Oriented Programming*

# Lesson 20 - Static Members, Static Blocks & Final Keyword

So far, every field and method we've written belonged to individual objects. This lesson introduces `static`, for members that belong to the class itself, and `final`, for values that can't change.

## 20.1 Static Fields

A **static field** belongs to the class as a whole, not to any individual object — there is exactly one copy, shared across every instance:

```java
public class Counter {
    static int totalCreated = 0;

    Counter() {
        totalCreated++;
    }
}

new Counter();
new Counter();
System.out.println(Counter.totalCreated); // 2
```

Static fields are accessed through the class name (`Counter.totalCreated`), not through an instance, though Java does technically allow the latter (it's discouraged, since it obscures that the field is shared).

---

## 20.2 Static Methods

A **static method** also belongs to the class rather than any instance, and can be called without creating an object. Because there's no specific object involved, static methods **cannot** access instance fields or call instance methods directly:

```java
public class MathUtils {
    static int square(int n) {
        return n * n;
    }
}

int result = MathUtils.square(5); // no object needed
```

You've already used static methods extensively — `Math.PI`, `Arrays.sort()`, and `main()` itself are all static.

---

## 20.3 Static Initializer Blocks

A **static initializer block** runs once, automatically, when the class is first loaded — useful for complex static field setup that can't be done in a single line:

```java
public class Config {
    static Map<String, String> settings;

    static {
        settings = new HashMap<>();
        settings.put("env", "production");
        settings.put("version", "1.0");
    }
}
```

This runs during classloading (see [Lesson 3](./[3]-How-Java-Works.md)), before any instance of the class is created.

---

## 20.4 The final Keyword

`final` means "cannot be reassigned or overridden," and applies differently depending on context:

- **`final` variable** — its value can only be assigned once.
- **`final` method** — cannot be overridden by a subclass.
- **`final` class** — cannot be extended/subclassed at all.

```java
final double PI_APPROX = 3.14159; // cannot reassign
public final void criticalMethod() { ... } // cannot override
public final class ImmutablePoint { ... } // cannot subclass
```

A very common pattern is combining `static` and `final` to declare true constants:

```java
public static final double GRAVITY = 9.81;
```

---

## 20.5 Static vs Instance Context

The most common beginner mistake with `static` is trying to access an instance field or method from a static context (like `main`) without an object reference — this fails to compile, because there's no specific object to refer to:

```java
public class Example {
    int instanceField = 10;

    public static void main(String[] args) {
        System.out.println(instanceField); // ERROR: no instance exists here
        // Fix: create an instance first
        Example e = new Example();
        System.out.println(e.instanceField); // works
    }
}
```

Keeping this distinction clear — "does this belong to the class, or to a specific object?" — resolves most confusion around `static`.

---

[Previous](./[19]-The-Object-Class.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[21]-Nested-and-Inner-Classes.md)