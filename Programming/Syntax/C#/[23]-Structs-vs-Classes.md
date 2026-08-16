[Previous](./[22]-Generics.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[24]-Delegates-Events-and-Lambdas.md)

*Object-Oriented Programming*

# Lesson 23 - Structs vs Classes (value vs reference types)

## 23.1 What is a Struct?

A `struct` looks almost identical to a `class` in how you define it, but it's a **value type** rather than a reference type:

```csharp
public struct Point
{
    public int X;
    public int Y;

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }
}

Point p1 = new Point(1, 2);
```

Small, simple data types like `Point`, `DateTime`, and the built-in numeric types are structs.

---

## 23.2 Value vs Reference Semantics

This is the core difference introduced back in [Lesson 5](./[5]-Variables-and-Data-Types.md), and it matters most when copying or passing values around:

```csharp
// struct — copying creates an independent value
Point p1 = new Point(1, 2);
Point p2 = p1;
p2.X = 99;
Console.WriteLine(p1.X);   // 1 — p1 is untouched

// class — copying just copies the reference
public class PointClass { public int X, Y; }

PointClass c1 = new PointClass { X = 1, Y = 2 };
PointClass c2 = c1;
c2.X = 99;
Console.WriteLine(c1.X);   // 99 — c1 and c2 point to the same object
```

Structs are also stored differently in memory (typically on the stack, or inline within their containing object) which makes them cheaper to create and destroy for small pieces of data, while classes live on the heap and are managed by the garbage collector (see [Lesson 31](./[31]-Memory-Management.md)).

---

## 23.3 When to Use Which

| Use a `struct` when... | Use a `class` when... |
|---|---|
| The type is small (a few fields) | The type is larger or complex |
| It represents a single value (a point, a color, a measurement) | It represents an entity with identity |
| Immutability is desired/easy to maintain | The object's state is expected to change over time |
| Value-copy semantics are what you want | Shared references are what you want |

When in doubt, default to a `class` — most everyday types in C# (and most of the .NET Base Class Library) are reference types, and structs are best reserved for small, immutable, value-like data.

[Previous](./[22]-Generics.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[24]-Delegates-Events-and-Lambdas.md)
