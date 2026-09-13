[Previous](./[3]-Nodes-And-The-Scene-Tree.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[5]-Signals-And-Communication-Between-Nodes.md)

*Core Concepts*

# Lesson 4 - GDScript Fundamentals

## 4.1 GDScript Syntax Basics

GDScript is Godot's built-in scripting language, designed specifically to work with the node/scene system. Its syntax is intentionally close to Python: it uses indentation instead of curly braces, and skips semicolons.

A script is attached to a node and, by convention, extends that node's type:

```gdscript
extends CharacterBody2D

func _ready():
    print("Player has entered the scene!")
```

- `extends` declares which built-in class this script builds on top of — here, the script gains everything a `CharacterBody2D` already does, plus whatever custom code you add.
- `func` defines a function, just like `def` in Python.
- Indentation (not braces) defines code blocks, so consistent spacing matters.
- `#` starts a comment.

Scripts are saved as `.gd` files and attached to a node via the **Attach Script** button (the scroll icon) in the Scene Dock, which also creates the file and links it automatically.

---

## 4.2 Variables, Types, And Export

Variables are declared with `var`:

```gdscript
var health = 100
var player_name = "Hero"
var speed: float = 250.0   # optional type hint
```

GDScript is dynamically typed by default, but supports optional **static typing** with a colon (`: float`, `: String`, `: Array`) — this isn't required, but it helps the editor catch mistakes and gives you autocomplete.

The `@export` annotation is one of the most useful features for beginners: it exposes a variable to the **Inspector**, so you (or a designer on your team) can tweak it without touching code:

```gdscript
extends CharacterBody2D

@export var speed: float = 250.0
@export var jump_force: float = 400.0
@export var max_health: int = 100
```

With this, `speed`, `jump_force`, and `max_health` now appear as editable fields on the node in the Inspector — perfect for balancing gameplay by trial and error without reopening the script every time.

---

## 4.3 Functions And The _ready/_process Callbacks

Godot calls certain functions automatically if you define them — these are **callbacks**, and the two you'll use constantly are:

- **`_ready()`** — called once, when the node and all its children have entered the scene tree. This is where you typically set up initial state.
- **`_process(delta)`** — called every rendered frame. `delta` is the time (in seconds) since the last frame, used to make movement framerate-independent.

```gdscript
extends CharacterBody2D

@export var speed: float = 250.0
var direction := Vector2.ZERO

func _ready():
    print("Ready! Starting position: ", position)

func _process(delta):
    direction = Vector2.ZERO
    if Input.is_action_pressed("ui_right"):
        direction.x += 1
    if Input.is_action_pressed("ui_left"):
        direction.x -= 1

    position += direction * speed * delta
```

Multiplying by `delta` is essential: without it, movement speed would depend on the player's framerate — a character would move twice as far per second on a 120fps monitor as on a 60fps one. There's also `_physics_process(delta)`, called at a fixed rate rather than tied to rendering, used for physics-driven movement — covered in Lesson 7.

You can define your own functions too, and call them from anywhere the node is visible:

```gdscript
func take_damage(amount: int):
    max_health -= amount
    if max_health <= 0:
        queue_free()   # removes this node from the scene
```

---

## 4.4 GDScript vs C# In Godot

Godot supports both GDScript and C# as first-class scripting languages (with the .NET build — see Lesson 1.1), and you can even mix them in the same project.

| | GDScript | C# |
|---|---|---|
| **Syntax style** | Python-like, indentation-based | C-like, brace-based, statically typed |
| **Setup** | Works out of the box | Requires the .NET SDK and .NET build of Godot |
| **Performance** | Fast enough for most 2D/gameplay logic | Generally faster for CPU-heavy code |
| **Editor integration** | Deepest — built specifically for Godot's API | Very good, but slightly more setup/tooling overhead |
| **Best for** | Beginners, rapid iteration, most gameplay code | Programmers coming from Unity/C#, performance-critical systems |

For this Topic, and for most people learning Godot for the first time, **GDScript is the better starting point** — it has the tightest integration with the editor (autocomplete for nodes, live-reload, built-in debugger support) and the smallest amount of setup. You can always introduce C# later for specific performance-sensitive systems once you're comfortable with how Godot's node/scene model works.

[Previous](./[3]-Nodes-And-The-Scene-Tree.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[5]-Signals-And-Communication-Between-Nodes.md)
