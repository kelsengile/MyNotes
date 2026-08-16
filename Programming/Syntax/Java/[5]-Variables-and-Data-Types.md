[Previous](./[4]-Build-Tools-Maven-and-Gradle.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)

*Core Syntax*

# Lesson 5 - Variables & Basic Data Types (Primitives vs. Reference Types)

With the environment set up, it's time to start learning Java syntax itself. This lesson introduces variables — the basic containers for data — and Java's type system.

## 5.1 Declaring Variables

Java is **statically typed**: every variable must have a declared type, known at compile time. To declare a variable, write the type, a name, and optionally an initial value:

```java
int age = 25;
double price;
price = 19.99;
```

Once a variable's type is set, it cannot change — you can't later assign a `String` to an `int` variable.

---

## 5.2 Primitive Types

Java has eight **primitive types**, which store raw values directly (not references to objects):

| Type | Size | Example |
|---|---|---|
| `byte` | 8-bit | `byte b = 10;` |
| `short` | 16-bit | `short s = 200;` |
| `int` | 32-bit | `int i = 100000;` |
| `long` | 64-bit | `long l = 100000L;` |
| `float` | 32-bit | `float f = 3.14f;` |
| `double` | 64-bit | `double d = 3.14159;` |
| `char` | 16-bit | `char c = 'A';` |
| `boolean` | 1 bit (conceptually) | `boolean flag = true;` |

Primitives are fast and lightweight because they're stored directly, without object overhead. We'll explore numeric, character, and boolean primitives in more depth in the next lesson.

---

## 5.3 Reference Types

Everything that isn't a primitive is a **reference type** — this includes `String`, arrays, and every object created from a class. A reference variable doesn't hold the object itself; it holds a reference (essentially a pointer) to where the object lives in memory.

```java
String name = "Alice";
int[] numbers = new int[5];
```

An important consequence: reference variables can be `null`, meaning "points to nothing," while primitives cannot.

```java
String middleName = null; // valid
```

---

## 5.4 Type Inference with var

Since Java 10, you can use `var` to let the compiler infer a variable's type from its initializer, instead of writing it explicitly:

```java
var count = 10;          // inferred as int
var name = "Alice";      // inferred as String
```

`var` doesn't make Java dynamically typed — the type is still fixed at compile time, it's just written for you. `var` requires an initializer and can't be used for fields or method parameters.

---

## 5.5 Naming Conventions

Java has strong, widely-followed naming conventions:

- Variables and methods use **camelCase**: `firstName`, `calculateTotal`.
- Classes use **PascalCase**: `BankAccount`, `Main`.
- Constants (`static final`) use **SCREAMING_SNAKE_CASE**: `MAX_SIZE`.

Following these conventions makes your code instantly readable to other Java developers, even though the compiler itself doesn't enforce them.

---

[Previous](./[4]-Build-Tools-Maven-and-Gradle.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)