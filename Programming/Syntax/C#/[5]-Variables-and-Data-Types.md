[Previous](./[4]-Configuration-and-Environment.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)

*Core Syntax*

# Lesson 5 - Variables & Basic Data Types

## 5.1 Declaring Variables

A variable stores a value under a name. In C#, you declare a variable by writing its type, then its name:

```csharp
int age = 25;
string name = "Ava";
double price = 19.99;
bool isActive = true;
```

C# is **statically typed** — once a variable is declared with a type, it can only hold values of that type.

---

## 5.2 Type Inference with `var`

The `var` keyword lets the compiler infer the type from the assigned value. The variable is still strongly typed — `var` is just a shorthand for the compiler to figure out the type at compile time, not a way to make it dynamic.

```csharp
var age = 25;       // inferred as int
var name = "Ava";   // inferred as string
```

`var` requires an initial value, since the compiler needs something to infer the type from.

---

## 5.3 Value Types vs Reference Types

- **Value types** (`int`, `double`, `bool`, `struct`) store their data directly. Copying one copies the value.
- **Reference types** (`string`, `class`, arrays) store a reference (pointer) to data on the heap. Copying one copies the reference, so both variables point to the same underlying object.

```csharp
int a = 5;
int b = a;   // b is a separate copy
b = 10;      // a is still 5
```

This distinction becomes especially important later, in [Lesson 23 - Structs vs Classes](./[23]-Structs-vs-Classes.md).

---

## 5.4 Constants

Use `const` for values that never change and are known at compile time:

```csharp
const double Pi = 3.14159;
```

Constants must be assigned a value at declaration and can never be reassigned afterward.

[Previous](./[4]-Configuration-and-Environment.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)
