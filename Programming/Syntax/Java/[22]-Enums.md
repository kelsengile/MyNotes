[Previous](./[21]-Nested-and-Inner-Classes.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[23]-Records.md)

*Object-Oriented Programming*

# Lesson 22 - Enums

## 22.1 What Is an Enum?

An **enum** (short for enumeration) is a special Java type used to represent a fixed set of constants — values that are known in advance and don't change, like days of the week, directions, or order statuses.

```java
public enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}
```

Under the hood, each enum constant is a full-fledged object, and the enum type itself implicitly extends `java.lang.Enum`. This gives enums type safety that plain constants (like `public static final int MONDAY = 0;`) don't have — the compiler won't let you assign anything to a `Day` variable except one of the declared constants.

---

## 22.2 Enum Constants and Basic Usage

Enum constants are declared once and reused everywhere. Comparison can be done safely with `==` since each constant is a single shared instance.

```java
Day today = Day.WEDNESDAY;

if (today == Day.SATURDAY || today == Day.SUNDAY) {
    System.out.println("It's the weekend!");
} else {
    System.out.println("It's a weekday.");
}
```

---

## 22.3 Enums with Fields, Constructors, and Methods

Enums can carry data and behavior just like regular classes. A constructor runs once per constant, when the enum is first loaded.

```java
public enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    VENUS(4.869e+24, 6.0518e6),
    EARTH(5.976e+24, 6.37814e6);

    private final double mass;
    private final double radius;

    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }

    double surfaceGravity() {
        final double G = 6.67300E-11;
        return G * mass / (radius * radius);
    }
}
```

```java
for (Planet planet : Planet.values()) {
    System.out.println(planet + " gravity: " + planet.surfaceGravity());
}
```

Enum constructors are always implicitly `private` — you can't create additional instances of an enum type from outside it with `new`.

---

## 22.4 Enums with Constant-Specific Bodies

Individual constants can override a method to give each one its own behavior, by supplying a class body after the constant.

```java
public enum Operation {
    ADD {
        public int apply(int a, int b) { return a + b; }
    },
    SUBTRACT {
        public int apply(int a, int b) { return a - b; }
    },
    MULTIPLY {
        public int apply(int a, int b) { return a * b; }
    };

    public abstract int apply(int a, int b);
}
```

```java
int result = Operation.ADD.apply(3, 4); // 7
```

This pattern is useful when every constant behaves differently enough that a single shared method body with a switch statement would be harder to read.

---

## 22.5 Enum Methods: values(), valueOf(), ordinal(), name()

Every enum automatically gets several useful methods for free:

```java
Day[] allDays = Day.values();          // array of all constants, in declared order
Day day = Day.valueOf("FRIDAY");       // parses a String into the matching constant
int position = Day.MONDAY.ordinal();   // 0 (position in declaration order)
String label = Day.MONDAY.name();      // "MONDAY"
```

- `values()` returns every constant in declaration order — handy for iterating.
- `valueOf(String)` throws an `IllegalArgumentException` if the name doesn't match any constant exactly.
- `ordinal()` gives the zero-based position; avoid relying on it for business logic, since inserting a new constant shifts every ordinal after it.
- `name()` returns the constant's exact declared name.

---

## 22.6 Enums in Switch Statements

Enums pair naturally with `switch`, since the compiler already knows every possible value.

```java
switch (today) {
    case SATURDAY, SUNDAY -> System.out.println("Weekend");
    default -> System.out.println("Weekday");
}
```

Note that case labels use the bare constant name (`SATURDAY`), not `Day.SATURDAY` — the compiler infers the enum type from the switch's subject.

---

## 22.7 Implementing Interfaces with Enums

Enums can implement interfaces, which is a clean way to guarantee that every constant provides certain behavior.

```java
interface Describable {
    String describe();
}

public enum Status implements Describable {
    ACTIVE {
        public String describe() { return "Currently active"; }
    },
    INACTIVE {
        public String describe() { return "Not active"; }
    };
}
```

Because an enum can't extend another class (it already implicitly extends `Enum`), implementing an interface is the main way to share a contract between an enum and other types in your codebase.

[Previous](./[21]-Nested-and-Inner-Classes.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[23]-Records.md)