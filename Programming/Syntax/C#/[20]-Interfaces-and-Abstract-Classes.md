[Previous](./[19]-Encapsulation-and-Access-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[21]-Properties-and-Indexers.md)

*Object-Oriented Programming*

# Lesson 20 - Interfaces & Abstract Classes

## 20.1 Interfaces

An **interface** defines a contract — a set of members that any implementing class must provide — without supplying any implementation itself:

```csharp
public interface IShape
{
    double GetArea();
}

public class Circle : IShape
{
    public double Radius;

    public double GetArea() => Math.PI * Radius * Radius;
}

public class Square : IShape
{
    public double Side;

    public double GetArea() => Side * Side;
}
```

A class can implement **multiple** interfaces, letting it satisfy several unrelated contracts at once:

```csharp
public class Circle : IShape, IComparable<Circle>
{
    // ...
}
```

---

## 20.2 Abstract Classes

An **abstract class** is a base class that can't be instantiated directly and may mix fully-implemented members with `abstract` members that derived classes *must* override:

```csharp
public abstract class Shape
{
    public string Color;                      // shared, concrete field

    public abstract double GetArea();          // no implementation — subclasses must provide one

    public void Describe()                     // shared, concrete method
    {
        Console.WriteLine($"A {Color} shape with area {GetArea()}");
    }
}

public class Circle : Shape
{
    public double Radius;
    public override double GetArea() => Math.PI * Radius * Radius;
}
```

`Shape shape = new Shape();` would fail to compile — only concrete subclasses like `Circle` can be instantiated.

---

## 20.3 Interfaces vs Abstract Classes

| | Interface | Abstract Class |
|---|---|---|
| Implementation | None (until C# 8's default methods) | Can include shared, working code |
| Fields | Not allowed | Allowed |
| Inheritance | A class can implement many | A class can inherit from only one |
| Best for | Defining a capability/contract | Sharing common code across related types |

**Rule of thumb:** use an interface to describe *what* something can do (`ICanFly`, `IComparable`); use an abstract class when related types share meaningful, reusable implementation (`Shape` → `Circle`, `Square`).

[Previous](./[19]-Encapsulation-and-Access-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[21]-Properties-and-Indexers.md)
