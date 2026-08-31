[Previous](./[4]-Prototyping-and-Game-Design-Documents.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./%5B6%5D-Control-Flow.md)

*Core Programming Concepts*

# Lesson 5 - Variables, Data Types & Operators

## 5.1 What Is a Variable?

A variable is a named piece of storage in memory that holds a value your code can read and change. In game code, variables track everything from a player's health to the current score:

```csharp
int health = 100;
float speed = 5.5f;
bool isJumping = false;
string playerName = "Hero";
```

Declaring a variable reserves memory and gives it a name and (in most languages used for games, like C# or C++) a fixed **type**.

---

## 5.2 Common Data Types

- **int** — whole numbers (e.g. score, lives, ammo count).
- **float** — decimal numbers (e.g. speed, position, health percentage). Most physics and movement math uses floats.
- **bool** — true/false flags (e.g. `isGrounded`, `isDead`).
- **string** — text (e.g. player names, dialogue lines).
- **Vector2 / Vector3** — engine-provided types bundling 2 or 3 float values together, used for positions and directions (see Lesson 13).

Choosing the right type matters: using an `int` for health means you can't have fractional healing, while a `float` allows smooth regeneration over time.

---

## 5.3 Operators

- **Arithmetic**: `+ - * / %` — the `%` (modulo) operator is especially useful in games for wrapping values, like cycling through an inventory hotbar.
- **Comparison**: `== != < > <= >=` — used constantly in conditionals (Lesson 6).
- **Logical**: `&& || !` — combine multiple conditions, e.g. `if (isGrounded && !isDead)`.
- **Assignment shortcuts**: `+= -= *= /=` — common for incrementing score or reducing health: `health -= damage;`.

---

## 5.4 Naming Conventions for Game Code

Consistent naming makes a growing codebase far easier to navigate:

- Use descriptive names: `playerHealth`, not `x`.
- Follow your engine/language's convention (C# in Unity typically uses `camelCase` for local variables and `PascalCase` for public fields and methods).
- Prefix booleans with `is`, `has`, or `can` (`isAlive`, `hasKey`, `canDoubleJump`) so their meaning is obvious at a glance.
- Avoid abbreviations that aren't universally clear (`plyrHlth` vs `playerHealth`).

[Previous](./[4]-Prototyping-and-Game-Design-Documents.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./%5B6%5D-Control-Flow.md)
