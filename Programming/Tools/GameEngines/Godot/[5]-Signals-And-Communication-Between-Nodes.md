[Previous](./[4]-GDScript-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[6]-2D-And-3D-Workflows-In-Godot.md)

*Core Concepts*

# Lesson 5 - Signals And Communication Between Nodes

## 5.1 What Is A Signal

A **signal** is Godot's built-in way of letting one node announce "something happened" without needing to know who — if anyone — is listening. It's Godot's implementation of the observer pattern, and it's the recommended way for nodes to talk to each other, especially across different branches of the scene tree.

Many built-in nodes already emit signals you can use immediately. A `Button`, for example, emits `pressed` whenever it's clicked; an `Area2D` emits `body_entered` whenever a physics body enters its shape; a `Timer` emits `timeout` when it finishes counting down.

The key idea: the node **emitting** a signal doesn't need a reference to the node **receiving** it. This keeps nodes decoupled — a `HealthComponent` can emit `health_depleted` without knowing (or caring) whether a `GameOverScreen`, a `SoundManager`, or nothing at all is listening.

---

## 5.2 Connecting Signals In The Editor And In Code

There are two ways to connect a signal to a function:

**In the editor:** select the emitting node, open the **Node** tab (next to the Inspector), double-click a signal in the list, and pick which node/method should receive it. Godot will auto-generate a matching function stub in that node's script.

**In code**, using `connect()`:

```gdscript
# In a script attached to the Level node
func _ready():
    $RestartButton.pressed.connect(_on_restart_button_pressed)

func _on_restart_button_pressed():
    get_tree().reload_current_scene()
```

Here, `$RestartButton` is a shortcut for fetching a child node by its name (short for `get_node("RestartButton")`), and `.pressed` is that button's built-in signal. Connecting in code is often preferred once a project grows, since it's easy to see all of a script's connections in one place, and it works well with nodes that are instanced dynamically at runtime rather than placed by hand in the editor.

---

## 5.3 Custom Signals

You aren't limited to built-in signals — you can declare and emit your own with the `signal` keyword:

```gdscript
extends Node

signal health_changed(new_health: int)
signal health_depleted

var health: int = 100

func take_damage(amount: int):
    health -= amount
    health_changed.emit(health)

    if health <= 0:
        health_depleted.emit()
```

Any other node can then connect to these exactly like a built-in signal:

```gdscript
# In a HealthBar UI script
func _ready():
    $"../HealthComponent".health_changed.connect(_on_health_changed)

func _on_health_changed(new_health: int):
    $ProgressBar.value = new_health
```

Custom signals are especially useful for communicating **upward or sideways** in the scene tree — for example, a bullet telling the level manager it hit something, without the bullet needing a direct reference back to the level manager.

---

## 5.4 Groups And Node Communication Patterns

Signals are the preferred way for a node to notify others of something that happened to *it*. But sometimes you need the opposite: to reach *many* nodes at once, regardless of where they live in the tree. That's what **groups** are for.

Any node can be added to one or more named groups, either in the editor (**Node > Groups** tab) or in code:

```gdscript
func _ready():
    add_to_group("enemies")
```

Once grouped, you can call a method on every member at once, from anywhere:

```gdscript
func _on_bomb_exploded():
    get_tree().call_group("enemies", "take_damage", 50)
```

This one line finds every node in the `"enemies"` group and calls their `take_damage(50)` method — no manual iteration, and no direct references required.

**Choosing the right tool:**

| Situation | Use |
|---|---|
| A node needs to tell its parent/listeners something happened to it | **Signal** |
| A node needs to call a method on a specific child it already knows about | Direct reference (`$ChildName`) |
| A node needs to affect many unrelated nodes at once | **Group** |
| A value needs to be shared globally across scenes | **Autoload / Singleton** (a global script, set up in Project Settings) |

A good rule of thumb: **signals flow up and outward, direct calls flow down.** A parent can safely call methods on its own children directly, but children should generally avoid holding direct references to their parents — emit a signal instead, and let whoever's interested connect to it.

[Previous](./[4]-GDScript-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[6]-2D-And-3D-Workflows-In-Godot.md)
