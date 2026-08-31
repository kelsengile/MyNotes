[Previous](./%5B5%5D-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./%5B7%5D-Functions-and-Scope.md)

*Core Programming Concepts*

# Lesson 6 - Control Flow: Conditionals & Loops

## 6.1 Conditional Statements

Conditionals let your game react differently depending on the current state:

```csharp
if (health <= 0) {
    Die();
} else if (health < 20) {
    ShowLowHealthWarning();
} else {
    // healthy
}
```

`switch` statements are useful when checking one variable against many possible discrete values, such as handling different `GameState` enums (menu, playing, paused, gameOver).

---

## 6.2 Loops

Loops repeat a block of code:

- **for** — best when you know how many times to repeat, such as iterating over a fixed-size inventory array.
- **while** — repeats as long as a condition holds, such as spawning enemies while `enemiesRemaining > 0`.
- **foreach** — iterates over every item in a collection (see Lesson 9), such as looping through all active enemies to update their AI.

```csharp
for (int i = 0; i < inventorySize; i++) {
    CheckSlot(i);
}

foreach (Enemy enemy in activeEnemies) {
    enemy.UpdateAI();
}
```

Be careful with `while` loops driven by conditions that might never become false — an infinite loop inside a single frame will freeze or crash your game.

---

## 6.3 Using Control Flow in Gameplay Code

Control flow is the backbone of gameplay logic. A few real examples:

- **State checks**: `if (isPaused) return;` at the top of an update function to skip logic entirely while paused.
- **Cooldown timers**: `if (Time.time >= nextFireTime) { Shoot(); }`.
- **Difficulty scaling**: looping through a wave list and spawning more enemies as the player's level increases.

Keeping conditionals shallow (avoiding deeply nested `if` inside `if` inside `if`) makes gameplay code easier to debug — consider early returns (`if (!isAlive) return;`) instead of wrapping the rest of a function in a large `else` block.

[Previous](./%5B5%5D-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./%5B7%5D-Functions-and-Scope.md)
