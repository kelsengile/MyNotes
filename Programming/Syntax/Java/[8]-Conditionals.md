[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[9]-Loops.md)

*Core Syntax*

# Lesson 8 - Conditionals: if, else if, else, switch (incl. switch expressions)

Conditionals let your program make decisions and take different paths depending on data. This lesson covers Java's two conditional constructs: `if`/`else` and `switch`.

## 8.1 if, else if, else

The `if` statement runs a block of code only when its condition is `true`:

```java
int score = 85;

if (score >= 90) {
    System.out.println("A");
} else if (score >= 80) {
    System.out.println("B");
} else {
    System.out.println("C or below");
}
```

Conditions are checked top to bottom, and only the first matching branch runs. The final `else` is optional and catches anything not matched above.

---

## 8.2 switch Statements

`switch` is useful when you're comparing a single value against several possible constants. The traditional form uses `case` labels and requires `break` to prevent **fall-through** into the next case:

```java
int day = 3;
switch (day) {
    case 1:
        System.out.println("Monday");
        break;
    case 2:
        System.out.println("Tuesday");
        break;
    default:
        System.out.println("Some other day");
}
```

Forgetting `break` is a classic source of bugs — execution "falls through" and runs the next case's code too, unless that's intentionally what you want (e.g. grouping multiple cases together).

---

## 8.3 Switch Expressions (Java 14+)

Modern Java added **switch expressions**, using `->` instead of `:`, which don't fall through and can directly produce a value:

```java
int day = 3;
String name = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    default -> "Unknown";
};
```

This form is shorter, safer (no accidental fall-through), and preferred in modern Java code whenever you're mapping a value to a result.

---

## 8.4 Pattern Matching in switch

Recent Java versions also let `switch` match on an object's **type or shape**, not just constant values — for example, testing what kind of object a variable holds:

```java
Object obj = "hello";
String description = switch (obj) {
    case Integer i -> "an integer: " + i;
    case String s -> "a string: " + s;
    default -> "something else";
};
```

This is a preview of a broader feature called **pattern matching**, which we'll explore fully once we've covered classes and interfaces, in [Lesson 24](./[24]-Sealed-Classes-and-Pattern-Matching.md).

---

[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[9]-Loops.md)