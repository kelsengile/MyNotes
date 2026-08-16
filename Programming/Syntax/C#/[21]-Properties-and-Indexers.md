[Previous](./[20]-Interfaces-and-Abstract-Classes.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[22]-Generics.md)

*Object-Oriented Programming*

# Lesson 21 - Properties, Indexers & Operator Overloading

## 21.1 Properties

A **property** looks like a field from the outside but is backed by `get`/`set` logic, letting a class control how its data is read and written:

```csharp
public class Person
{
    private string _name;

    public string Name
    {
        get => _name;
        set => _name = value ?? "Unknown";
    }

    // Auto-property: compiler generates the hidden backing field for you
    public int Age { get; set; }

    // Read-only property, only settable from inside the class
    public string Id { get; private set; } = Guid.NewGuid().ToString();
}
```

Properties are the standard, idiomatic way to expose a class's data in C# — plain public fields are generally avoided outside of simple data-holder types.

---

## 21.2 Indexers

An **indexer** lets objects of a class be accessed with `[]` syntax, just like an array:

```csharp
public class Roster
{
    private List<string> _names = new List<string>();

    public string this[int index]
    {
        get => _names[index];
        set
        {
            while (_names.Count <= index) _names.Add(null);
            _names[index] = value;
        }
    }
}

var roster = new Roster();
roster[0] = "Ava";
Console.WriteLine(roster[0]);   // "Ava"
```

---

## 21.3 Operator Overloading

**Operator overloading** lets you define what operators like `+`, `-`, or `==` mean for your own types:

```csharp
public struct Vector2
{
    public double X, Y;

    public static Vector2 operator +(Vector2 a, Vector2 b)
        => new Vector2 { X = a.X + b.X, Y = a.Y + b.Y };
}

Vector2 v1 = new Vector2 { X = 1, Y = 2 };
Vector2 v2 = new Vector2 { X = 3, Y = 4 };
Vector2 sum = v1 + v2;   // { X = 4, Y = 6 }
```

Use operator overloading sparingly, and only when the operator's meaning is genuinely intuitive for the type (like adding two mathematical vectors) — overusing it can make code harder to read.

[Previous](./[20]-Interfaces-and-Abstract-Classes.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[22]-Generics.md)
