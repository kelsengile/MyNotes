[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[8]-Conditionals.md)

*Core Syntax*

# Lesson 7 - Operators & Expressions (arithmetic, comparison, logical, null-coalescing)

## 7.1 Arithmetic Operators

```csharp
int sum = 5 + 3;   // 8
int diff = 5 - 3;  // 2
int prod = 5 * 3;  // 15
int quot = 7 / 2;  // 3  (integer division truncates)
int rem = 7 % 2;   // 1  (remainder / modulo)
```

Dividing two `int`s discards the decimal part. To get `3.5`, at least one operand must be a floating-point type: `7.0 / 2`.

---

## 7.2 Comparison Operators

Comparison operators return a `bool`:

```csharp
5 == 5   // true  (equal to)
5 != 3   // true  (not equal to)
5 > 3    // true
5 < 3    // false
5 >= 5   // true
5 <= 3   // false
```

---

## 7.3 Logical Operators

Logical operators combine `bool` expressions:

```csharp
bool a = true, b = false;

a && b   // false — AND: both must be true
a || b   // true  — OR: at least one must be true
!a       // false — NOT: flips the value
```

`&&` and `||` **short-circuit** — if the left side already determines the result, the right side isn't evaluated at all.

---

## 7.4 Null-Coalescing Operators

The `??` operator provides a fallback value when something is `null`:

```csharp
string? name = null;
string display = name ?? "Guest";   // "Guest"
```

`??=` assigns only if the variable is currently `null`:

```csharp
string? nickname = null;
nickname ??= "Anonymous";   // nickname is now "Anonymous"
```

The `?.` **null-conditional** operator safely accesses a member only if the object isn't null, returning `null` instead of throwing:

```csharp
int? length = name?.Length;   // null, since name is null
```

[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[8]-Conditionals.md)
