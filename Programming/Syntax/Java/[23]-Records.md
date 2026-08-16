[Previous](./[22]-Enums.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[24]-Sealed-Classes-and-Pattern-Matching.md)

*Object-Oriented Programming*

# Lesson 23 - Records

## 23.1 What Is a Record?

A **record** is a special kind of class, introduced as a standard feature in Java 16, designed to model plain, immutable data. Instead of writing a constructor, fields, getters, `equals()`, `hashCode()`, and `toString()` by hand, you declare the data once and the compiler generates all of it for you.

```java
public record Point(int x, int y) {}
```

That single line gives you a fully working immutable class with a constructor, accessor methods, and correct `equals()`, `hashCode()`, and `toString()` implementations — recall from Lesson 19 how much boilerplate overriding those methods normally takes.

---

## 23.2 Declaring and Using Records

The values listed in parentheses after the record name are called **components**. Each component becomes a `private final` field and gets a public accessor method with the same name (not prefixed with `get`).

```java
public record Point(int x, int y) {}
```

```java
Point origin = new Point(0, 0);
Point p = new Point(3, 4);

System.out.println(p.x());       // 3
System.out.println(p.y());       // 4
System.out.println(p);           // Point[x=3, y=4]
System.out.println(p.equals(origin)); // false
```

Records are implicitly `final` — they cannot be extended — and every component is implicitly `final`, so once a record instance is created, its data can't change.

---

## 23.3 Compact Constructors

Sometimes you need to validate or normalize the data passed into a record. A **compact constructor** lets you do that without restating every parameter.

```java
public record Range(int min, int max) {
    public Range {
        if (min > max) {
            throw new IllegalArgumentException("min cannot be greater than max");
        }
    }
}
```

Notice there's no parameter list or `this.min = min;` assignments — the compact constructor runs your validation logic, and the normal field assignment happens automatically afterward. You can also reassign a parameter inside a compact constructor to normalize it before it's stored:

```java
public record Username(String value) {
    public Username {
        value = value.trim().toLowerCase();
    }
}
```

---

## 23.4 Custom Methods and Static Members

Records can define additional methods, static fields, and static methods, just like a regular class.

```java
public record Point(int x, int y) {
    static final Point ORIGIN = new Point(0, 0);

    double distanceTo(Point other) {
        int dx = x - other.x;
        int dy = y - other.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

```java
double d = new Point(3, 4).distanceTo(Point.ORIGIN); // 5.0
```

What records **cannot** do is declare additional instance fields beyond their components, or provide a no-argument constructor that skips setting them — a record's identity is always fully defined by its components.

---

## 23.5 Records vs Traditional Classes

| | Record | Traditional Class |
|---|---|---|
| Mutability | Always immutable | Mutable by default |
| Boilerplate | Auto-generated constructor, accessors, `equals()`, `hashCode()`, `toString()` | Written by hand |
| Inheritance | Implicitly `final`, can't extend another class | Can extend and be extended |
| Best for | Plain data carriers (DTOs, value objects, API responses) | Objects with identity, mutable state, or complex behavior |

Use a record when a type's entire job is to hold a fixed bundle of related values — a coordinate, a money amount, a database row, a request payload. Reach for a regular class when the type needs mutable state, inheritance, or represents something with behavior beyond its data.

---

## 23.6 A Preview: Records and Pattern Matching

Because a record's structure is fixed and known at compile time, Java lets you "deconstruct" one directly inside a pattern — pulling out its components without calling the accessor methods yourself:

```java
if (obj instanceof Point(int x, int y)) {
    System.out.println("x=" + x + ", y=" + y);
}
```

This is called a **record pattern**, and it becomes especially powerful when combined with sealed classes and `switch`. The next lesson covers exactly that.

[Previous](./[22]-Enums.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[24]-Sealed-Classes-and-Pattern-Matching.md)