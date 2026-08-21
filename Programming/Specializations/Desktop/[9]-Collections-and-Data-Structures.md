[Previous](./[8]-Object-Oriented-Basics.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[10]-Windows-Dialogs-and-App-Lifecycle.md)

*Core Syntax*

# Lesson 9 - Collections & Data Structures

## 9.1 Arrays and Lists

Arrays have a fixed size decided at creation; lists (`List<T>` in C#, `Vec<T>` in Rust, plain arrays in JS) grow and shrink dynamically. Desktop apps use lists constantly — a list of open documents, a list of rows in a grid, a list of items in a dropdown.

```csharp
var openTabs = new List<string>();
openTabs.Add("index.html");
openTabs.Remove("index.html");
```

---

## 9.2 Dictionaries / Maps

A dictionary stores key-value pairs and offers fast lookup by key — ideal for things like application settings (`"theme" -> "dark"`) or caching loaded resources by file path.

```csharp
var settings = new Dictionary<string, string> { ["theme"] = "dark" };
string theme = settings["theme"];
```

---

## 9.3 Sets, Stacks, and Queues

- **Sets** store unique values with fast membership checks — useful for tracking which files have unsaved changes.
- **Stacks** (LIFO) power undo/redo history: each edit pushes onto an undo stack; undoing pops it off and pushes onto a redo stack.
- **Queues** (FIFO) are a natural fit for background task pipelines, where work items are processed in the order they arrived.

---

## 9.4 Choosing the Right Structure

Pick a structure based on how you'll access the data: need order-preserving iteration → list; need fast lookup by a unique key → dictionary; need to know "have I seen this before?" → set; need "undo the last action" → stack. Choosing the wrong structure is a common source of avoidable performance problems in desktop apps that manage large in-memory datasets (covered further in Lesson 44).

[Previous](./[8]-Object-Oriented-Basics.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[10]-Windows-Dialogs-and-App-Lifecycle.md)
