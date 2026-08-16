[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[11]-String-Formatting.md)

*Core Syntax*

# Lesson 10 - Methods & Scope (overloading, varargs, pass-by-value)

Methods let you package up code into reusable, named blocks. This lesson covers how to define them, pass data in and out, and understand where variables are visible.

## 10.1 Defining Methods

A method has a return type, a name, and a parameter list, followed by a body in braces:

```java
public static int add(int a, int b) {
    return a + b;
}
```

If a method doesn't return a value, its return type is `void`. We're using `static` here since these examples run outside of an object context — we'll explain what that means fully once we reach [Lesson 20](./[20]-Static-and-Final.md).

---

## 10.2 Parameters and Return Values

**Parameters** are the inputs a method accepts; **arguments** are the actual values passed in when it's called. A method's `return` statement sends a value back to the caller and immediately exits the method:

```java
public static double calculateArea(double radius) {
    return Math.PI * radius * radius;
}

double area = calculateArea(5.0); // argument: 5.0
```

---

## 10.3 Method Overloading

Java lets you define multiple methods with the **same name** but different parameter lists — this is called **overloading**. The compiler picks the right version based on the arguments you pass:

```java
public static int add(int a, int b) {
    return a + b;
}

public static double add(double a, double b) {
    return a + b;
}
```

Overloads must differ in the number or types of parameters — return type alone isn't enough to distinguish them.

---

## 10.4 Varargs

**Varargs** (variable-length arguments) let a method accept any number of arguments of a given type, using `...`:

```java
public static int sum(int... numbers) {
    int total = 0;
    for (int n : numbers) {
        total += n;
    }
    return total;
}

sum(1, 2, 3);       // 6
sum(1, 2, 3, 4, 5); // 15
```

Inside the method, `numbers` behaves like a regular array. A method can have at most one varargs parameter, and it must be the last one listed.

---

## 10.5 Pass-by-Value Semantics

Java is **strictly pass-by-value** — always. When you call a method, a *copy* of each argument's value is passed in:

- For **primitives**, the copy is the actual value, so changes inside the method don't affect the caller's original variable.
- For **reference types**, the copy is the reference itself (the "address"), so the method can modify the *object* the reference points to, but reassigning the parameter to a new object has no effect outside the method.

```java
public static void increment(int x) {
    x++; // does NOT affect the caller's variable
}
```

This distinction trips up many newcomers — it's worth revisiting once we cover objects and arrays in more depth.

---

## 10.6 Variable Scope

**Scope** determines where in your code a variable is visible and usable. A variable declared inside a method (or a block, like a loop or `if`) only exists within that block:

```java
public static void example() {
    int x = 10;
    if (true) {
        int y = 20; // y only exists inside this block
    }
    // y is not accessible here
}
```

Once execution leaves a block, its local variables are discarded. Understanding scope prevents bugs where you accidentally expect a variable to still exist outside the block it was declared in.

---

[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[11]-String-Formatting.md)