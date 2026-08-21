[Previous](./[7]-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[9]-Screens-Views-and-Layouts.md)

*Core Syntax*

# Lesson 8 - Collections: Arrays, Lists & Maps

## 8.1 Arrays and Lists

An ordered collection of items — the backbone of almost every mobile screen that shows more than one thing (a feed, a product list, a contact book).

```dart
List<String> fruits = ["Apple", "Banana", "Cherry"];
fruits.add("Mango");
print(fruits[0]); // "Apple"
```

Lists are typically **generic** (`List<String>`, `List<User>`), meaning the compiler enforces that every item is the same type, preventing accidental mixing.

---

## 8.2 Maps / Dictionaries

A collection of key-value pairs, used for looking up data by an identifier — for example, caching users by their ID:

```swift
var userCache: [String: User] = [:]
userCache["u123"] = User(name: "Alex")
print(userCache["u123"]?.name)
```

```kotlin
val settings: Map<String, Boolean> = mapOf("darkMode" to true, "notifications" to false)
```

---

## 8.3 Sets

An unordered collection of **unique** values — useful when you only care about membership, not order or duplicates (e.g., a set of selected item IDs in a multi-select list).

```dart
Set<int> selectedIds = {1, 2, 3};
selectedIds.add(2); // no effect, already present
```

---

## 8.4 Functional Collection Operations

Mobile languages provide functional-style methods for transforming collections without manual loops — these are used constantly when preparing data for display:

```dart
final names = users.map((u) => u.name).toList();
final adults = users.where((u) => u.age >= 18).toList();
final total = prices.fold(0.0, (sum, p) => sum + p);
```

- **`map`** transforms each item.
- **`where`/`filter`** keeps only items matching a condition.
- **`fold`/`reduce`** combines all items into a single value.

Learning to reach for these instead of manual `for` loops makes data-transformation code shorter and less error-prone, and it's the idiomatic style you'll see in real mobile codebases.

[Previous](./[7]-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[9]-Screens-Views-and-Layouts.md)
