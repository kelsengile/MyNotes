[Previous](./%5B6%5D-Control-Flow%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./%5B8%5D-Object-Oriented-Basics%20%281%29.md)

*Core Programming Concepts*

# Lesson 7 - Functions & Scope

## 7.1 What Is a Function?

A function (also called a method) is a named, reusable block of code. Instead of copy-pasting damage logic everywhere it's needed, you write it once:

```csharp
void TakeDamage(int amount) {
    health -= amount;
    if (health <= 0) {
        Die();
    }
}
```

Functions make code easier to read, test, and change — fixing a bug in one function fixes it everywhere that function is called.

---

## 7.2 Parameters and Return Values

- **Parameters** are the inputs a function accepts (`amount` above).
- **Return values** are what a function sends back to whoever called it:

```csharp
bool IsAlive() {
    return health > 0;
}
```

Functions that return `void` perform an action but don't hand back a value; functions with a specific return type must always return a value of that type on every code path.

---

## 7.3 Scope: Local vs Global

**Scope** determines where a variable is visible and how long it lives.

- **Local variables** are declared inside a function and only exist while that function runs (e.g. a temporary `distance` variable used mid-calculation).
- **Member/instance variables** belong to an object and persist for as long as that object exists (e.g. `health` on a `Player` class).
- **Global/static variables** are accessible from anywhere in the codebase. They're convenient but easy to misuse — overusing globals makes bugs harder to trace because any code, anywhere, could be changing that value.

---

## 7.4 Functions in Game Engine Callbacks

Engines call certain functions automatically at specific points in the game loop (see Lesson 10). Common examples:

- `Start()` / `_ready()` — runs once when an object is created.
- `Update()` / `_process(delta)` — runs every frame.
- `FixedUpdate()` / `_physics_process(delta)` — runs on a fixed timestep, used for physics.
- `OnCollisionEnter()` / `_on_body_entered()` — runs when a collision occurs.

Understanding which callback runs when — and how often — is essential for writing gameplay code that behaves predictably.

[Previous](./%5B6%5D-Control-Flow%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./%5B8%5D-Object-Oriented-Basics%20%281%29.md)
