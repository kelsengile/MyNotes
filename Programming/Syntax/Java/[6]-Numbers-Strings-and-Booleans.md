[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[7]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 6 - Numbers, Strings, Characters & Booleans

Now that you know the basic type categories, let's dig deeper into the everyday types you'll use constantly: numbers, text, and true/false logic.

## 6.1 Integer Types in Depth

Java offers four whole-number types, differing only in size (and therefore range): `byte`, `short`, `int`, and `long`. `int` is the default choice for whole numbers unless you specifically need a larger range, in which case use `long` (suffixing the literal with `L`):

```java
int population = 8_000_000;      // underscores improve readability
long worldPopulation = 8_000_000_000L;
```

Integer division truncates any remainder: `7 / 2` evaluates to `3`, not `3.5`.

---

## 6.2 Floating-Point Types in Depth

`float` and `double` represent numbers with a fractional component. `double` is the default and generally preferred, since it offers more precision; `float` literals need an `f` suffix:

```java
double price = 19.99;
float weight = 2.5f;
```

Floating-point numbers cannot represent all decimal values exactly (due to binary representation), so avoid using them for exact values like currency — a `BigDecimal`, covered later, is more appropriate there.

---

## 6.3 The char Type

`char` stores a single 16-bit Unicode character, written in single quotes:

```java
char grade = 'A';
char newline = '\n';
```

Because `char` is really a numeric type under the hood, you can perform arithmetic on it — `'A' + 1` evaluates to `66`, the numeric code for `'B'`.

---

## 6.4 The String Type

`String` represents text and is a reference type (not a primitive), written in double quotes:

```java
String greeting = "Hello, world!";
```

Strings support concatenation with `+`:

```java
String fullName = "Ada" + " " + "Lovelace";
```

Java `String`s are **immutable** — once created, their content never changes. Every "modifying" operation actually returns a brand-new `String`. We'll cover `String` methods and manipulation in much more depth in [Lesson 11](./[11]-String-Formatting.md).

---

## 6.5 The boolean Type

`boolean` holds one of exactly two values: `true` or `false`. It's the backbone of conditional logic:

```java
boolean isActive = true;
boolean hasPermission = false;
```

Unlike some languages, Java does **not** treat `0` or `null` as automatically "falsy" — a `boolean` expression must genuinely evaluate to `true` or `false`. This strictness helps prevent a whole category of bugs common in loosely-typed languages.

---

[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[7]-Operators-and-Expressions.md)