[Previous](./[3]-Popular-Game-Engines.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[5]-GameObjects-And-Components.md)

*World And Object Management*

# Lesson 4 - Scenes And The Game World

## 4.1 What Is a Scene

A **scene** is a self-contained snapshot of a game world at a moment in time — a level, a menu screen, or a single room can each be its own scene. A scene holds a collection of objects (characters, lights, cameras, terrain, UI elements) along with their properties and relationships.

Almost every engine treats the scene as the fundamental unit of content organization. Instead of building an entire game as one giant, monolithic world, developers split it into scenes such as `MainMenu`, `Level1`, `Level2`, and `GameOverScreen`. Each scene can be authored, tested, and loaded independently.

**Example project structure for a small platformer:**

```
Scenes/
├── MainMenu.scene
├── Level1_Forest.scene
├── Level2_Cave.scene
├── Level3_Castle.scene
└── GameOverScreen.scene
```

Each `.scene` file is self-contained, so a designer could open and test `Level2_Cave` directly without ever loading the main menu or any other level.

---

## 4.2 Scene Graphs

Internally, most engines represent a scene as a **scene graph** — a tree-like structure where every object is a node, and nodes can contain other nodes as children. This mirrors how objects relate to each other in the real world: a character node might have a "weapon" node attached to its "hand," so that when the hand moves, the weapon moves with it automatically.

A simplified scene graph might look like this:

```
Scene: Level1
├── Player
│   ├── Camera
│   └── Weapon
├── Enemy_01
├── Enemy_02
└── Environment
    ├── Terrain
    ├── Tree_01
    └── Tree_02
```

The scene graph isn't just an organizational convenience — it directly affects how transformations (position, rotation, scale) propagate, which we'll cover in depth in Lesson 6.

---

## 4.3 Loading And Unloading Scenes

Games rarely load every scene into memory at once — that would be enormously wasteful, since a game might have dozens of levels but the player is only ever in one at a time. Instead, engines provide functions to **load** a scene (bringing all its objects and assets into memory and initializing them) and **unload** it (freeing that memory when it's no longer needed).

Common loading strategies include:

- **Single scene loading** — one scene is active at a time; loading a new one unloads the previous one, often with a loading screen in between.
- **Additive loading** — multiple scenes are loaded simultaneously and layered together, useful for streaming large open worlds in pieces or keeping persistent UI/HUD scenes active across level changes.
- **Asynchronous loading** — a scene loads in the background while the player continues playing (or watches a loading animation), avoiding a hard freeze.

| Strategy | Memory usage | Best for |
|---|---|---|
| Single scene | Low (one scene at a time) | Linear levels, menus |
| Additive | Higher (multiple scenes at once) | Persistent HUD, streaming open worlds |
| Asynchronous | Varies | Any game that wants to avoid hard freezes |

---

## 4.4 A Simple Scene Example

Consider a simple 2D platformer level. Its scene might contain:

- A `Player` object with a sprite, a collider, and a script controlling movement.
- Several `Platform` objects, each with a static collider so the player can stand on them.
- A `Coin` object placed multiple times throughout the level, each awarding points when collected.
- A `Camera` object that follows the player.
- A `LevelExit` trigger zone that loads the next scene when the player reaches it.

In an engine's editor, this scene would be built visually: dragging a `Player` prefab into the world, positioning platforms by hand, and previewing the result instantly by pressing "play" — all without writing a rendering or physics system, since the engine already provides those subsystems described in Lesson 2.

---

[Previous](./[3]-Popular-Game-Engines.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[5]-GameObjects-And-Components.md)
