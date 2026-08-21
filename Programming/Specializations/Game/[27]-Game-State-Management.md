[Previous](./[26]-Pathfinding.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[28]-Inventory-and-Item-Systems.md)

*Game Systems & Logic*

# Lesson 27 - Game State Management

## 27.1 What Is Game State?

**Game state** is all the data describing "where things currently stand" — whether the game is at the main menu, actively playing, paused, or showing a game-over screen; the player's score, health, and position; which level is loaded. Managing state cleanly is what keeps a growing game from turning into a tangle of scattered flags and special cases.

---

## 27.2 State Machines for Game Flow

Just as NPCs use finite state machines (Lesson 25), the overall **flow** of a game benefits from the same pattern at a higher level:

```
MainMenu → (Play pressed) → Loading → (level loaded) → Playing
Playing → (pause pressed) → Paused → (resume pressed) → Playing
Playing → (player dies) → GameOver → (retry pressed) → Loading
```

Each state controls what input is accepted, what's visible on screen, and what logic runs — for example, gameplay `Update` logic typically checks `if (currentState != GameState.Playing) return;` at the top, so nothing moves while the game is paused or on a menu.

---

## 27.3 Global vs Scene-Level State

- **Scene-level state** lives within a single scene and is reset/discarded when that scene unloads (e.g. the position of enemies within a level).
- **Global state** needs to persist across scene transitions (e.g. total score, unlocked abilities, player inventory).

A common technique is a **persistent manager object** — a single object marked to survive scene loads, holding global state and providing a central place other scripts can reference (e.g. `GameManager.Instance.score += 10;`), rather than every script needing to somehow reach across scene boundaries.

---

## 27.4 Persisting State Across Scenes

To carry information from one scene into the next, common approaches include:

- **Persistent manager objects** — as above, an object that isn't destroyed on scene load, holding shared data in memory.
- **ScriptableObjects / shared data assets** — engine-specific data containers that exist independently of any scene and can be read/written by scripts in any scene.
- **Save/load to disk** — writing state to a file and reading it back, which also enables saving progress between play sessions entirely (explored fully in Lesson 30).

Choosing the right mechanism depends on whether the data only needs to survive a scene transition within the same play session, or needs to persist even after the game is closed and reopened.

[Previous](./[26]-Pathfinding.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[28]-Inventory-and-Item-Systems.md)
