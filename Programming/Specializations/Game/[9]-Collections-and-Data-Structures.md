[Previous](./%5B8%5D-Object-Oriented-Basic.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[10]-The-Game-Loop.md)

*Core Programming Concepts*

# Lesson 9 - Collections & Data Structures for Games

## 9.1 Arrays and Lists

- **Arrays** have a fixed size decided when created — good for things with a known, constant count (e.g. `Vector3[3] spawnPoints`).
- **Lists** (dynamic arrays) can grow or shrink at runtime — ideal for things like `List<Enemy> activeEnemies`, where the count changes as enemies spawn and die.

```csharp
List<Enemy> activeEnemies = new List<Enemy>();
activeEnemies.Add(newEnemy);
activeEnemies.Remove(deadEnemy);
```

---

## 9.2 Dictionaries / Maps

A dictionary stores **key-value pairs**, letting you look up a value instantly by its key instead of searching through a list:

```csharp
Dictionary<string, int> itemPrices = new Dictionary<string, int>();
itemPrices["sword"] = 50;
itemPrices["shield"] = 30;

int cost = itemPrices["sword"]; // instant lookup, no searching
```

Dictionaries are ideal for inventories, save data, and looking up game objects by ID.

---

## 9.3 Queues and Stacks

- **Queue** — first-in, first-out (FIFO). Useful for things like an enemy spawn queue, processed in the order they were added.
- **Stack** — last-in, first-out (LIFO). Useful for things like an "undo" system, or tracking a chain of active menus (opening a submenu pushes onto the stack; pressing back pops it off).

---

## 9.4 Choosing the Right Structure

| Need | Structure |
|---|---|
| Fixed-size, known count | Array |
| Growing/shrinking collection | List |
| Fast lookup by name/ID | Dictionary |
| Process items in the order received | Queue |
| Undo/back navigation | Stack |

Picking the right structure isn't just about correctness — it affects performance. Searching for an item in a `List` of thousands of enemies every frame is much slower than looking it up directly in a `Dictionary`, which matters once a game has many active objects.

[Previous](./%5B8%5D-Object-Oriented-Basics.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[10]-The-Game-Loop.md)
