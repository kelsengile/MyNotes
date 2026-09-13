[Previous](./[6]-2D-And-3D-Workflows-In-Godot.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[8]-UI-With-Control-Nodes.md)

*Building A Game*

# Lesson 7 - Physics And Collision In Godot

## 7.1 CharacterBody, RigidBody, And StaticBody

Godot has three main physics body types (in both 2D and 3D flavors: `CharacterBody2D`/`3D`, `RigidBody2D`/`3D`, `StaticBody2D`/`3D`), and picking the right one is the most important physics decision you'll make for any given object.

- **`StaticBody`** — never moves (or moves in ways physics shouldn't react to, like a moving platform). Used for walls, floors, and level geometry. Other bodies collide against it, but it doesn't respond to collisions itself.

- **`RigidBody`** — fully simulated by the physics engine: affected by gravity, forces, and collisions automatically, the way a real object would be. Best for objects you want to behave "naturally" — crates, rocks, ragdolls, anything you want to push around and have bounce/tumble realistically without scripting the motion yourself.

- **`CharacterBody`** — designed specifically for player- and enemy-controlled characters. Unlike `RigidBody`, it does *not* respond to forces automatically — you move it entirely through code, using its built-in `move_and_slide()` method, which handles sliding along walls and floors for you.

```gdscript
extends CharacterBody2D

const SPEED = 300.0
const GRAVITY = 980.0

func _physics_process(delta):
    velocity.y += GRAVITY * delta

    var direction = Input.get_axis("ui_left", "ui_right")
    velocity.x = direction * SPEED

    move_and_slide()
```

`move_and_slide()` reads the built-in `velocity` property, moves the body, and automatically stops it against walls/floors rather than pushing through them — this one method covers the vast majority of what a platformer or top-down controller needs.

---

## 7.2 Collision Shapes And Layers/Masks

A physics body only actually collides with something once it has a **shape** — added as a child `CollisionShape2D`/`3D` node holding a `Shape` resource (`RectangleShape2D`, `CircleShape2D`, `CapsuleShape3D`, etc.). Without one, a body is invisible to physics entirely.

```
Player (CharacterBody2D)
├── Sprite2D
└── CollisionShape2D  ← holds a CapsuleShape2D resource
```

**Layers and masks** control *which* bodies can collide with *which* — without them, everything would collide with everything, which quickly becomes both wasteful and hard to reason about.

- **Collision Layer** — which layer(s) *this* body belongs to.
- **Collision Mask** — which layer(s) *this* body checks against/reacts to.

For example, you might set up layers like:

| Layer # | Name | Used By |
|---|---|---|
| 1 | `world` | Walls, floors, static geometry |
| 2 | `player` | The player character |
| 3 | `enemies` | Enemy characters |
| 4 | `player_projectiles` | Bullets fired by the player |

A player's bullet would set its **Layer** to `player_projectiles` and its **Mask** to `world` + `enemies` — meaning it collides with walls and enemies, but critically *not* with the player who fired it, avoiding self-damage bugs without any extra code. This layer/mask system is set per-node in the Inspector under the **Collision** section.

---

## 7.3 Area Nodes For Triggers

Sometimes you don't want a solid collision response — you want to *detect* overlap without blocking movement. That's what `Area2D`/`Area3D` are for: they emit signals when bodies enter or exit their shape, but never physically stop anything.

Common uses: pickups, damage zones, level-transition triggers, cutscene starters.

```gdscript
extends Area2D

func _ready():
    body_entered.connect(_on_body_entered)

func _on_body_entered(body):
    if body.is_in_group("player"):
        queue_free()   # collect the pickup
        # e.g. SoundManager.play("pickup")
```

Like physics bodies, Areas also use collision layers/masks — a `player_pickups` layer, for instance, checked only by the player's mask, so enemies walking over a coin doesn't do anything.

---

## 7.4 The _physics_process Loop

You may have noticed the movement code above used `_physics_process(delta)` rather than `_process(delta)` from Lesson 4. This isn't a stylistic choice — it matters for correctness.

- **`_process(delta)`** runs once per *rendered frame*, so its rate varies with your framerate (30fps, 60fps, 144fps, whatever the player's machine achieves).
- **`_physics_process(delta)`** runs at a **fixed rate** (60 times per second by default, configurable in Project Settings), independent of rendering framerate.

Physics simulation needs a fixed, predictable timestep to stay stable and deterministic — running collision detection at an inconsistent rate can cause bodies to tunnel through walls, jitter, or behave differently on different hardware. The rule of thumb:

| Use `_physics_process` for... | Use `_process` for... |
|---|---|
| Any movement involving `move_and_slide()`, physics bodies, or raycasts | UI updates, animations, camera smoothing |
| Anything that needs to interact consistently with collision | Anything purely visual/cosmetic |

Mixing the two is fine and common — a player script might move the body in `_physics_process` while a separate UI script updates a health bar in `_process` — the key is matching the right callback to whether the code touches physics.

[Previous](./[6]-2D-And-3D-Workflows-In-Godot.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[8]-UI-With-Control-Nodes.md)
