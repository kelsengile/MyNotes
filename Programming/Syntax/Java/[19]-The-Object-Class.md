[Previous](./[18]-Abstraction-Interfaces-and-Abstract-Classes.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[20]-Static-and-Final.md)

*Object-Oriented Programming*

# Lesson 19 - The Object Class & Overriding equals(), hashCode(), toString()

As mentioned in Lesson 16, every class in Java implicitly extends `Object`. This lesson explores the useful methods it provides, and why overriding them correctly matters.

## 19.1 Every Class Extends Object

Whether you write `extends Object` or not, every class inherits from it, which is why methods like `toString()`, `equals()`, and `hashCode()` are always available on any object — even one from a class you just wrote with no explicit inheritance.

---

## 19.2 toString()

`toString()` returns a `String` representation of an object, used automatically whenever an object is printed or concatenated with a string. The default implementation is not very useful — it just prints the class name and a memory hash:

```java
public class Point {
    int x, y;
    Point(int x, int y) { this.x = x; this.y = y; }
}

System.out.println(new Point(1, 2)); // Point@1b6d3586 (not helpful)
```

Override it to produce something meaningful:

```java
@Override
public String toString() {
    return "Point(" + x + ", " + y + ")";
}
// now prints: Point(1, 2)
```

---

## 19.3 equals()

By default, `equals()` (inherited from `Object`) compares **references** — it returns `true` only if two variables point to the exact same object, identical to `==`. This is usually not what you want when comparing two objects for logical equality:

```java
Point p1 = new Point(1, 2);
Point p2 = new Point(1, 2);
System.out.println(p1.equals(p2)); // false by default — different objects!
```

Override it to compare meaningful field values instead:

```java
@Override
public boolean equals(Object obj) {
    if (this == obj) return true;
    if (!(obj instanceof Point)) return false;
    Point other = (Point) obj;
    return this.x == other.x && this.y == other.y;
}
```

---

## 19.4 hashCode()

`hashCode()` returns an `int` used by hash-based collections like `HashMap` and `HashSet` to efficiently bucket objects. The default implementation derives a value from the object's memory address:

```java
@Override
public int hashCode() {
    return Objects.hash(x, y);
}
```

`java.util.Objects.hash(...)` is the standard, convenient way to combine multiple fields into one hash code.

---

## 19.5 The equals/hashCode Contract

Java requires a strict rule: **if two objects are equal according to `equals()`, they must return the same `hashCode()`.** Breaking this contract causes subtle, hard-to-debug problems — for example, a `HashSet` may silently store "duplicate" entries because it looked in the wrong hash bucket.

The safe practice is: **always override `equals()` and `hashCode()` together**, based on the same set of fields, never just one or the other. Most IDEs can generate correct, consistent implementations of both automatically.

---

[Previous](./[18]-Abstraction-Interfaces-and-Abstract-Classes.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[20]-Static-and-Final.md)