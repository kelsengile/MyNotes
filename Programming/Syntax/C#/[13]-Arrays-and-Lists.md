[Previous](./[12]-Exception-Handling.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[14]-Dictionaries-and-Sets.md)

*Data Structures*

# Lesson 13 - Arrays & Lists

## 13.1 Arrays

An array holds a **fixed-size** collection of elements, all of the same type:

```csharp
int[] numbers = { 1, 2, 3, 4, 5 };
string[] names = new string[3];   // fixed size of 3, all default to null

Console.WriteLine(numbers[0]);    // 1
numbers[0] = 10;                  // arrays are mutable
Console.WriteLine(numbers.Length); // 5
```

Once created, an array's size can't change — adding a sixth element requires creating a new, larger array.

---

## 13.2 List\<T\>

`List<T>` is a resizable collection from `System.Collections.Generic` — the `T` is a placeholder for whatever type the list holds (see [Lesson 22 - Generics](./[22]-Generics.md)). It's the most commonly used collection type in everyday C# code:

```csharp
using System.Collections.Generic;

List<string> names = new List<string>();
names.Add("Ava");
names.Add("Ben");
names.Remove("Ava");
Console.WriteLine(names.Count);   // 1

List<int> scores = new List<int> { 90, 85, 78 };
```

---

## 13.3 Common Operations

```csharp
List<int> numbers = new List<int> { 5, 3, 8, 1 };

numbers.Sort();                    // [1, 3, 5, 8]
numbers.Contains(3);               // true
numbers.IndexOf(8);                // 2
numbers.Insert(0, 100);            // insert 100 at index 0
numbers.RemoveAt(1);               // remove item at index 1
int[] asArray = numbers.ToArray(); // convert to a regular array
```

Use an **array** when the size is fixed and known ahead of time; use a **`List<T>`** when items will be added or removed as the program runs.

[Previous](./[12]-Exception-Handling.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[14]-Dictionaries-and-Sets.md)
