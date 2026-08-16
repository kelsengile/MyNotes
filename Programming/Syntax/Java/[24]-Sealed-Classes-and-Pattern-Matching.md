[Previous](./[23]-Records.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[25]-Generics.md)

*Object-Oriented Programming*

# Lesson 24 - Sealed Classes And Pattern Matching

## 24.1 What Are Sealed Classes?

A **sealed class** (or interface) restricts which other classes are allowed to extend or implement it. Where a normal `public` class can be subclassed by anything, anywhere, a sealed class declares its complete, closed list of permitted subclasses up front.

This matters because it lets both you and the compiler reason about a type hierarchy as a known, finite set of possibilities — which becomes especially powerful when combined with pattern matching, covered later in this lesson.

```java
public sealed interface Shape permits Circle, Square, Rectangle {}
```

---

## 24.2 Declaring Sealed Classes and Interfaces

A sealed type uses the `sealed` modifier along with a `permits` clause listing its allowed direct subtypes.

```java
public sealed class Vehicle permits Car, Motorcycle, Truck {
    // shared fields/methods
}

public final class Car extends Vehicle {}
public final class Motorcycle extends Vehicle {}
public final class Truck extends Vehicle {}
```

If all the permitted subclasses are declared in the same file as the sealed type, the `permits` clause can be omitted — the compiler infers it from the file's contents.

---

## 24.3 Permitted Subclasses: final, sealed, non-sealed

Every class listed in a `permits` clause must choose exactly one of three modifiers, so the hierarchy's openness is always explicit:

- **`final`** — no further subclassing allowed at all.
- **`sealed`** — continues restricting its own subclasses with its own `permits` clause.
- **`non-sealed`** — reopens the hierarchy, allowing any class to extend it freely from that point on.

```java
public sealed interface Shape permits Circle, Square, Polygon {}

public final class Circle implements Shape {}
public final class Square implements Shape {}
public non-sealed interface Polygon extends Shape {}
```

Here, `Polygon` deliberately breaks the closed hierarchy — any class can now implement `Polygon`, even outside this codebase.

---

## 24.4 Pattern Matching for instanceof

Traditionally, using `instanceof` meant checking the type and then manually casting:

```java
if (obj instanceof String) {
    String s = (String) obj;
    System.out.println(s.toUpperCase());
}
```

Pattern matching for `instanceof` (standard since Java 16) lets you combine the check and the cast into one step:

```java
if (obj instanceof String s) {
    System.out.println(s.toUpperCase());
}
```

If the `instanceof` check fails, `s` simply isn't assigned and the block is skipped — the variable `s` is only usable where the compiler can prove the check succeeded.

---

## 24.5 Switch Expressions with Pattern Matching

Java 21 extended `switch` (from Lesson 8) to match on types directly, not just constant values:

```java
static String describe(Object obj) {
    return switch (obj) {
        case Integer i -> "An integer: " + i;
        case String s -> "A string of length " + s.length();
        case null -> "It's null";
        default -> "Something else";
    };
}
```

When the `switch`'s subject is a **sealed type**, the compiler already knows every possible subtype from the `permits` clause. That means a switch covering every permitted subtype is considered **exhaustive** and doesn't need a `default` branch at all:

```java
static double area(Shape shape) {
    return switch (shape) {
        case Circle c -> Math.PI * c.radius() * c.radius();
        case Square s -> s.side() * s.side();
        case Rectangle r -> r.width() * r.height();
    };
}
```

If a new subtype is later added to `Shape`'s `permits` clause, this `switch` will fail to compile until a matching case is added — turning a runtime bug (a missed case) into a compile-time error.

---

## 24.6 Record Patterns in Switch

Record patterns (introduced in Lesson 23) can be combined with `switch` to both match a type and destructure its components in one step.

```java
sealed interface Shape permits Circle, Rectangle {}
record Circle(double radius) implements Shape {}
record Rectangle(double width, double height) implements Shape {}

static double area(Shape shape) {
    return switch (shape) {
        case Circle(double r) -> Math.PI * r * r;
        case Rectangle(double w, double h) -> w * h;
    };
}
```

Patterns can also be nested, pulling values out of records that contain other records:

```java
record Point(int x, int y) {}
record Line(Point start, Point end) {}

static void printIfHorizontal(Object obj) {
    if (obj instanceof Line(Point(var x1, var y1), Point(var x2, var y2)) && y1 == y2) {
        System.out.println("Horizontal line at y=" + y1);
    }
}
```

---

## 24.7 Sealed Classes and Pattern Matching Together

Sealed types and pattern matching are designed to work as a pair:

- **Sealed classes** define a closed, known set of possible types.
- **Pattern matching** lets you branch on those types cleanly, with the compiler enforcing that every case is handled.

This combination gives Java a style of modeling similar to "algebraic data types" found in functional languages — you get compiler-checked exhaustiveness without needing the visitor pattern or long `instanceof`/cast chains that were previously the standard workaround. It's especially useful for representing things like API responses, AST nodes, or state machines, where you want the compiler to catch any unhandled case immediately.

[Previous](./[23]-Records.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[25]-Generics.md)