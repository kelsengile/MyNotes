[Previous](./[4]-Command-Line-Interfaces-and-Console-Apps.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[6]-Control-Flow.md)

*Core Syntax*

# Lesson 5 - Variables, Data Types & Operators

## 5.1 Declaring Variables

A variable is a named, typed storage location. Desktop languages generally fall into two camps:

- **Statically typed** (C#, C++, Swift, Rust): the type is fixed at compile time and checked before the program runs.
- **Dynamically typed** (JavaScript in Electron/Tauri UIs): the type is determined at runtime and can change.

```csharp
int count = 0;          // statically typed
string name = "Claude"; // statically typed
```

```javascript
let count = 0;      // dynamically typed
let name = "Claude"; // can later be reassigned to any type
```

---

## 5.2 Common Primitive Types

Across languages you'll consistently encounter: integers (`int`, `long`), floating-point numbers (`float`, `double`), booleans (`bool`), characters (`char`), and text (`string`). Desktop UI code frequently also uses framework-specific value types for geometry — points, sizes, and rectangles — since positioning windows and controls is a core part of the job.

---

## 5.3 Constants and Immutability

Marking a value as constant (`const` in C++/JS, `readonly`/`const` in C#, `let` bindings default to immutable in Rust) prevents accidental reassignment and documents intent. Favor immutability for configuration values and anything shared across threads, since immutable data can't be corrupted by concurrent writes — a concern that becomes important starting in Lesson 22.

---

## 5.4 Operators

Arithmetic (`+ - * / %`), comparison (`== != < > <= >=`), logical (`&& || !`), and assignment (`= += -= *=`) operators behave similarly across C-family languages. Watch for two gotchas that trip up beginners: integer division truncating decimals (`7 / 2 == 3` in many typed languages), and reference vs. value equality when comparing objects (`==` may compare identity, not contents, unless the type overrides it).

[Previous](./[4]-Command-Line-Interfaces-and-Console-Apps.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[6]-Control-Flow.md)
