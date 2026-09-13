[Previous](./[2]-The-Godot-Editor-Interface.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[4]-GDScript-Fundamentals.md)

*Core Concepts*

# Lesson 3 - Nodes And The Scene Tree

## 3.1 What Is A Node

A **node** is the fundamental building block of everything in Godot. A sprite is a node. A sound player is a node. A button, a light, a physics body, a timer — all nodes. Each node type inherits from a base class (`Node`) and adds its own specialized behavior and properties.

Every node has:

- A **name**, unique among its siblings (e.g. `Player`, `Sprite2D`, `HealthBar`).
- A **type**, which determines what it can do (`Sprite2D`, `AudioStreamPlayer`, `Button`, etc.).
- A set of **properties**, editable in the Inspector (position, texture, volume...).
- Optionally, a **script** attached to it, which adds custom behavior on top of the built-in type.

Godot's node classes form an inheritance hierarchy — for example, `Sprite2D` inherits from `Node2D`, which inherits from `CanvasItem`, which inherits from `Node`. This means every `Sprite2D` automatically has a position, rotation, and scale (from `Node2D`) and can be shown or hidden (from `CanvasItem`), without you having to add those features yourself.

---

## 3.2 Building A Scene Tree

Nodes don't exist in isolation — they're arranged into a **tree**, where every node (except one root) has exactly one parent. This is the **scene tree**, and it's the structure you see in the Scene Dock.

A simple player character might look like this:

```
Player (CharacterBody2D)
├── Sprite2D
├── CollisionShape2D
├── Camera2D
└── AnimationPlayer
```

Here, `Player` is a `CharacterBody2D` (a physics-driven node made for player/enemy movement), and everything underneath it is a **child** that moves along with it. Moving the `Player` node moves the sprite, the collision shape, and the camera together, because their positions are relative to their parent.

This parent-child relationship is the core organizing principle of Godot: **anything that should move, appear, or be destroyed together belongs under the same parent.**

---

## 3.3 Scenes As Reusable Building Blocks

A **scene** is simply a saved node tree — a `.tscn` file. Any branch of nodes, no matter how complex, can be saved as its own scene and reused elsewhere. This is how Godot avoids you rebuilding the same enemy, bullet, or UI element by hand every time you need one.

For example, you might build an `Enemy` scene once:

```
Enemy (CharacterBody2D)
├── Sprite2D
├── CollisionShape2D
└── HealthComponent
```

...save it as `enemy.tscn`, and then reuse it dozens of times across different levels without duplicating any node setup. If you later fix a bug in `enemy.tscn` — say, correcting its collision shape — every level using that enemy is fixed automatically, because they all reference the same saved scene.

Every Godot project has at least one **main scene**, set in Project Settings, which is the scene that runs first when the game starts.

---

## 3.4 Instancing Scenes Within Scenes

Scenes become truly powerful when you **instance** one scene inside another. An instance is a copy of a saved scene, placed as a node (or branch of nodes) within a different scene — and it can be further customized without changing the original.

For example, a `Level` scene might instance the `Enemy` scene three times:

```
Level (Node2D)
├── TileMap
├── Enemy (instance of enemy.tscn)
├── Enemy (instance of enemy.tscn)
├── Enemy (instance of enemy.tscn)
└── Player (instance of player.tscn)
```

Each `Enemy` instance can have its own position, and even its own overridden property values (like a different starting health), while still automatically inheriting any changes made to the original `enemy.tscn` file — a behavior/data mix similar to prefabs or components in other engines.

To instance a scene in the editor: select the parent node, click the **link icon** (Instance Child Scene) in the Scene Dock toolbar, and choose the `.tscn` file. You can also drag a scene file directly from the FileSystem Dock into the viewport or Scene Dock.

> **Why this matters:** thinking in scenes-as-components is the single biggest shift when moving from "just placing objects" to structuring an actual Godot project. A well-designed game is usually a small number of well-made reusable scenes, instanced many times, rather than one giant hand-built tree.

[Previous](./[2]-The-Godot-Editor-Interface.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[4]-GDScript-Fundamentals.md)
