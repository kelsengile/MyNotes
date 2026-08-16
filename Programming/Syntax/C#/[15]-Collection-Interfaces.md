[Previous](./[14]-Dictionaries-and-Sets.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[16]-Tuples-and-Records.md)

*Data Structures*

# Lesson 15 - Collection Interfaces (IEnumerable, ICollection, IList)

## 15.1 IEnumerable\<T\>

`IEnumerable<T>` is the most basic collection interface — it only guarantees that you can iterate over the items with `foreach`. Arrays, `List<T>`, `Dictionary<T>`, and nearly every collection type implement it:

```csharp
void PrintAll(IEnumerable<string> items)
{
    foreach (var item in items)
        Console.WriteLine(item);
}

PrintAll(new List<string> { "a", "b" });   // works
PrintAll(new[] { "a", "b" });              // also works
```

Writing methods that accept `IEnumerable<T>` instead of a specific collection type makes them reusable with any collection.

---

## 15.2 ICollection\<T\>

`ICollection<T>` extends `IEnumerable<T>` and adds the ability to modify and measure the collection: `Add`, `Remove`, `Clear`, and `Count`. `List<T>` and `HashSet<T>` both implement it.

```csharp
void AddItem(ICollection<string> items, string item)
{
    items.Add(item);
    Console.WriteLine(items.Count);
}
```

---

## 15.3 IList\<T\>

`IList<T>` extends `ICollection<T>` further, adding **index-based** access — `items[0]`, `Insert`, `RemoveAt`. `List<T>` and arrays both implement it, but `HashSet<T>` does not, since sets have no concept of position.

```csharp
void ReplaceFirst(IList<string> items, string newValue)
{
    if (items.Count > 0)
        items[0] = newValue;
}
```

**Rule of thumb:** accept the least specific interface your method actually needs (`IEnumerable<T>` for read-only iteration, `ICollection<T>` if you need to add/remove, `IList<T>` if you need indexing) — it keeps your code flexible about what callers can pass in.

[Previous](./[14]-Dictionaries-and-Sets.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[16]-Tuples-and-Records.md)
