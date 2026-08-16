[Previous](./[21]-Properties-and-Indexers.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[23]-Structs-vs-Classes.md)

*Object-Oriented Programming*

# Lesson 22 - Generics

## 22.1 Why Generics?

Without generics, you'd have to write nearly-identical code for every type you want to support, or give up type safety entirely and use `object`. **Generics** let you write a class or method once, using a placeholder type parameter, and reuse it safely for any type:

```csharp
// Without generics — only works for int, and loses type info if using object instead
public class IntBox
{
    public int Value;
}

// With generics — works for any type, and stays type-safe
public class Box<T>
{
    public T Value;
}

Box<int> intBox = new Box<int> { Value = 5 };
Box<string> stringBox = new Box<string> { Value = "hello" };
```

`List<T>` and `Dictionary<TKey, TValue>` from earlier lessons are themselves generic types — `T` is simply filled in with `string`, `int`, or whatever type you need.

---

## 22.2 Generic Classes

```csharp
public class Pair<T1, T2>
{
    public T1 First;
    public T2 Second;

    public Pair(T1 first, T2 second)
    {
        First = first;
        Second = second;
    }
}

var pair = new Pair<string, int>("Ava", 25);
```

---

## 22.3 Generic Methods

A single method can also be generic, independent of whether its containing class is:

```csharp
public T FirstOrDefault<T>(List<T> list)
{
    return list.Count > 0 ? list[0] : default(T);
}

int firstNumber = FirstOrDefault(new List<int> { 1, 2, 3 });     // 1
string firstName = FirstOrDefault(new List<string> { "Ava" });   // "Ava"
```

The compiler infers `T` from the arguments you pass, so you usually don't need to specify it explicitly.

---

## 22.4 Constraints

A **constraint** restricts what types can be used for `T`, using the `where` clause — useful when your generic code needs to call specific members that not every type has:

```csharp
public class Repository<T> where T : class, new()
{
    public T CreateNew() => new T();
}

public double Sum<T>(List<T> items) where T : struct, IComparable<T>
{
    // ...
}
```

Common constraints include `where T : class` (reference types only), `where T : struct` (value types only), `where T : new()` (must have a parameterless constructor), and `where T : SomeBaseClass` or `where T : ISomeInterface` (must inherit from or implement a given type).

[Previous](./[21]-Properties-and-Indexers.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[23]-Structs-vs-Classes.md)
