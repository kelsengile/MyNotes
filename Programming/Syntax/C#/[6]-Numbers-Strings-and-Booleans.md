[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[7]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 6 - Numbers, Strings & Booleans

## 6.1 Numeric Types

C# has separate types for whole numbers and decimal numbers:

| Type | Use for | Example |
|---|---|---|
| `int` | Whole numbers | `int count = 42;` |
| `long` | Very large whole numbers | `long big = 10000000000L;` |
| `double` | Decimal numbers (default) | `double price = 9.99;` |
| `decimal` | Precise decimals (money) | `decimal total = 9.99m;` |
| `float` | Lower-precision decimals | `float f = 3.14f;` |

Use `decimal` for currency, since `double` can introduce small rounding errors that matter when dealing with money.

---

## 6.2 Strings

A `string` holds text, always wrapped in double quotes:

```csharp
string greeting = "Hello, world!";
```

Strings are **immutable** — every operation that appears to modify a string actually creates a new one:

```csharp
string name = "Ava";
string upper = name.ToUpper(); // "AVA" — name itself is unchanged
```

---

## 6.3 Booleans

A `bool` holds only `true` or `false`, and is most often used in conditions:

```csharp
bool isLoggedIn = false;
if (isLoggedIn)
{
    Console.WriteLine("Welcome back!");
}
```

---

## 6.4 Type Conversion

Converting between types can be **implicit** (safe, automatic) or **explicit** (you must ask for it, because data could be lost):

```csharp
int i = 10;
double d = i;              // implicit: int -> double is always safe

double price = 9.75;
int rounded = (int)price;  // explicit cast: rounded == 9 (fraction is dropped)

string text = "123";
int parsed = int.Parse(text);          // throws if text isn't a valid number
int.TryParse(text, out int result);    // safer: returns false instead of throwing
```

[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[7]-Operators-and-Expressions.md)
