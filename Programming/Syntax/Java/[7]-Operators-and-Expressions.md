[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[8]-Conditionals.md)

*Core Syntax*

# Lesson 7 - Operators & Expressions

Operators combine values into expressions, letting you compute, compare, and transform data. This lesson tours the operator families Java provides.

## 7.1 Arithmetic Operators

Java's basic math operators work as you'd expect: `+`, `-`, `*`, `/`, and `%` (modulo/remainder).

```java
int sum = 5 + 3;       // 8
int remainder = 7 % 2; // 1
```

`++` and `--` increment or decrement a variable by one, and can be used as a prefix (`++x`) or postfix (`x++`) — the difference matters when the result is used immediately in an expression, since prefix updates before returning the value and postfix returns the value first.

---

## 7.2 Assignment Operators

Beyond plain `=`, Java offers compound assignment operators that combine an operation with assignment:

```java
int x = 10;
x += 5;  // same as x = x + 5;  -> 15
x -= 3;  // 12
x *= 2;  // 24
x /= 4;  // 6
```

---

## 7.3 Comparison Operators

These compare two values and produce a `boolean` result: `==`, `!=`, `<`, `>`, `<=`, `>=`.

```java
boolean isEqual = (5 == 5);   // true
boolean isGreater = (10 > 3); // true
```

Important: for reference types like `String`, `==` compares whether two variables point to the *same object*, not whether their content is equal — use `.equals()` for content comparison, which we'll revisit in [Lesson 19](./[19]-The-Object-Class.md).

---

## 7.4 Logical Operators

Logical operators combine `boolean` expressions: `&&` (AND), `||` (OR), and `!` (NOT).

```java
boolean canVote = age >= 18 && isCitizen;
boolean isWeekend = (day == "Saturday") || (day == "Sunday");
```

`&&` and `||` are **short-circuiting** — if the left side already determines the result, the right side isn't evaluated at all, which is useful for avoiding errors like null checks followed by method calls.

---

## 7.5 Bitwise Operators

Bitwise operators manipulate the individual bits of integer values: `&` (AND), `|` (OR), `^` (XOR), `~` (NOT), `<<` (left shift), `>>` (signed right shift), `>>>` (unsigned right shift).

```java
int a = 6;  // 0110
int b = 3;  // 0011
int result = a & b; // 0010 -> 2
```

These are used far less often in everyday application code, but show up in performance-sensitive code, flags, and low-level data processing.

---

## 7.6 The Ternary Operator

The ternary operator `? :` is a compact if/else that produces a value:

```java
int age = 20;
String status = (age >= 18) ? "adult" : "minor";
```

It's best reserved for short, simple conditions — anything more complex is more readable as a full `if`/`else` (covered next lesson).

---

## 7.7 Operator Precedence

When an expression mixes multiple operators, Java evaluates them in a defined order (similar to math class "order of operations"): multiplicative operators before additive, comparisons before logical operators, and so on. When in doubt, use parentheses `()` to make the intended order explicit — it costs nothing and prevents subtle bugs:

```java
int result = 2 + 3 * 4;      // 14, not 20
int clearer = 2 + (3 * 4);   // same result, clearer intent
```

---

[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[8]-Conditionals.md)