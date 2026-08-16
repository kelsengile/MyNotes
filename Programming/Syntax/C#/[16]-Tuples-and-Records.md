[Previous](./[15]-Collection-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[17]-OOP-Classes-and-Objects.md)

*Data Structures*

# Lesson 16 - Tuples & Records

## 16.1 Tuples

A **tuple** groups multiple values together without needing to define a dedicated type — handy for returning more than one value from a method:

```csharp
(string name, int age) GetPerson()
{
    return ("Ava", 25);
}

var person = GetPerson();
Console.WriteLine($"{person.name} is {person.age}");
```

Tuples are best for quick, internal groupings. For anything that represents a meaningful concept in your program — and especially anything shared across multiple methods — a named type is clearer.

---

## 16.2 Records

A `record` is a reference type designed for **immutable data**. It automatically generates useful behavior — value-based equality, a readable `ToString()`, and a concise constructor — that you'd otherwise have to write by hand on a class:

```csharp
public record Person(string Name, int Age);

var p1 = new Person("Ava", 25);
var p2 = new Person("Ava", 25);

Console.WriteLine(p1 == p2);      // true — compares values, not references
Console.WriteLine(p1);            // Person { Name = Ava, Age = 25 }
```

`with` expressions create a modified copy without mutating the original:

```csharp
var p3 = p1 with { Age = 26 };   // p1 is unchanged; p3 is a new Person
```

---

## 16.3 Records vs Classes

| | `class` | `record` |
|---|---|---|
| Equality | Reference-based (same object?) | Value-based (same data?) |
| Mutability | Usually mutable | Usually immutable |
| Best for | Objects with identity and behavior | Data that gets passed around and compared |

Use a `record` when a type mainly represents *data* (like a DTO or a value passed between layers of an app), and a `class` when it represents an *object* with behavior and identity. Classes are covered in depth starting in [Lesson 17](./[17]-OOP-Classes-and-Objects.md).

[Previous](./[15]-Collection-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[17]-OOP-Classes-and-Objects.md)
