[Previous](./[13]-Arrays-and-Lists.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[15]-Collection-Interfaces.md)

*Data Structures*

# Lesson 14 - Dictionaries & Sets

## 14.1 Dictionary\<TKey, TValue\>

A `Dictionary` stores **key-value pairs**, letting you look up a value quickly by its key instead of by index:

```csharp
using System.Collections.Generic;

Dictionary<string, int> ages = new Dictionary<string, int>
{
    ["Ava"] = 25,
    ["Ben"] = 30
};

ages["Cara"] = 28;               // add a new entry
Console.WriteLine(ages["Ava"]);  // 25

if (ages.TryGetValue("Dan", out int age))
{
    Console.WriteLine(age);
}
else
{
    Console.WriteLine("Not found");   // this runs — "Dan" isn't a key
}
```

Each key in a dictionary must be unique; assigning to an existing key overwrites its value.

---

## 14.2 HashSet\<T\>

A `HashSet<T>` stores a collection of **unique** values with no particular order — adding a duplicate is simply ignored:

```csharp
HashSet<string> tags = new HashSet<string> { "csharp", "dotnet" };

tags.Add("csharp");   // ignored — already present
tags.Add("web");      // added
Console.WriteLine(tags.Count);   // 3

tags.Contains("dotnet");   // true — HashSet lookups are very fast
```

---

## 14.3 Choosing the Right Collection

| Need | Use |
|---|---|
| Fixed-size, indexed items | `Array` |
| Resizable, ordered items (possible duplicates) | `List<T>` |
| Fast lookup by a unique key | `Dictionary<TKey, TValue>` |
| Fast membership checks, no duplicates | `HashSet<T>` |

[Previous](./[13]-Arrays-and-Lists.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[15]-Collection-Interfaces.md)
