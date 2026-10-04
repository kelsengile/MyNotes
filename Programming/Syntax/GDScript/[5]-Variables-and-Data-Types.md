[Previous](./[4]-Project-Structure-and-Settings.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)

*Core Syntax*

# Lesson 5 - Variables, Constants & Data Types

Programs work with data: a player's health, a name, a position, a list of items. A **variable** is a named place to store that data so you can use and change it. This lesson introduces variables and constants, the basic data types, how Godot checks them, and how to convert between them.

All the examples in this lesson can be placed inside a script like this:

```gdscript
extends Node


func _ready() -> void:
	# Example code goes here
	pass
```

---

## 5.1 What is a Variable? (`var`)

A variable is a **named container** for a value. You create one with the `var` keyword:

```gdscript
extends Node


func _ready() -> void:
	var health = 100
	var player_name = "Ada"
	var is_alive = true

	print(health)        # 100
	print(player_name)   # Ada
	print(is_alive)      # true
```

You can **change** a variable's value after creating it by assigning to it with `=`:

```gdscript
extends Node


func _ready() -> void:
	var score = 0
	score = 10
	score = score + 5
	print(score)   # 15
```

A variable can be declared **without** a value. It then holds `null`, which means "nothing":

```gdscript
extends Node


func _ready() -> void:
	var target
	print(target)   # <null>
```

### Where variables live

Variables can be declared in two main places:

```gdscript
extends Node

var speed = 300          # Member variable: belongs to the whole script (the node)


func _ready() -> void:
	var local_value = 5  # Local variable: only exists inside this function
	print(speed, " ", local_value)
```

- **Member variables** are declared at the top level of the script. Every function in the script can use them, and they last as long as the node exists.
- **Local variables** are declared inside a function. They exist only until the function ends.

Scope is covered in more detail in [Lesson 10](./[10]-Functions-and-Scope.md).

---

## 5.2 Naming Rules & Conventions (`snake_case`)

### Rules (the compiler enforces these)

- Names can contain **letters, digits, and underscores** (`_`).
- A name **cannot start with a digit**. `1st_place` is invalid, `first_place` is valid.
- Names are **case-sensitive**. `Score`, `score`, and `SCORE` are three different names.
- You **cannot use reserved keywords** such as `var`, `func`, `if`, `class`, or `signal` as names.
- No spaces or special characters such as `-`, `$`, or `!`.

### Conventions (the community style, recommended)

| Kind | Style | Example |
| --- | --- | --- |
| Variables and functions | `snake_case` | `max_health`, `get_damage()` |
| Constants | `CONSTANT_CASE` | `MAX_SPEED` |
| Classes and nodes | `PascalCase` | `PlayerStats`, `Sprite2D` |
| Signals | `snake_case`, past tense | `health_changed`, `died` |
| Private (internal use) members | Leading underscore | `_current_state` |

```gdscript
extends Node


func _ready() -> void:
	var max_health = 100          # Good
	var player_name = "Ada"       # Good
	var isAlive = true            # Works, but not the GDScript style
	print(max_health, player_name, isAlive)
```

Use **descriptive names**. `enemy_count` tells the reader far more than `n` or `x`.

---

## 5.3 Constants (`const`)

A **constant** is a value that is set once and can **never change**. Use constants for fixed values such as the maximum health, the speed of gravity, or a tile size.

```gdscript
extends Node

const MAX_HEALTH = 100
const GRAVITY = 980.0
const GAME_TITLE = "Space Shooter"
const TILE_SIZE = Vector2(16, 16)


func _ready() -> void:
	print(MAX_HEALTH)
	# MAX_HEALTH = 200   # ERROR: Cannot assign a new value to a constant.
```

Rules for constants:

- The value must be known when the script is **loaded** (it must be a "constant expression"). You can use literals, math on literals, and other constants.
- Constants can be of type `int`, `float`, `String`, `Vector2`, `Color`, arrays, dictionaries, and so on.
- You can compute them from other constants:

```gdscript
extends Node

const TILE = 16
const MAP_WIDTH = 20
const WORLD_WIDTH = TILE * MAP_WIDTH   # 320
const COLORS = ["red", "green", "blue"]
```

- Constants can be declared at the top level of a script only, which makes them members of the class. They can also be used inside functions.
- Constants can be loaded resources via `preload()`:

```gdscript
extends Node

const BULLET_SCENE = preload("res://bullet.tscn")
```

> **Note:** A constant array or dictionary cannot be reassigned, and in Godot 4 its contents are also read-only. Trying to append to a `const` array gives an error.

Why use constants?

1. **Readability:** `MAX_HEALTH` is clearer than a mysterious `100`.
2. **Safety:** The compiler stops you from accidentally changing them.
3. **One place to edit:** Change the value once and every use updates.

---

## 5.4 Dynamic vs Static Typing

GDScript lets you choose how strict you want to be about types.

### Dynamic typing (the default)

If you write only `var x = 5`, the variable has **no fixed type**. It can hold any kind of value, and its type can change while the game runs:

```gdscript
extends Node


func _ready() -> void:
	var thing = 10         # An int right now
	thing = "ten"          # Now a String
	thing = Vector2(1, 2)  # Now a Vector2
	print(thing)
```

This is flexible and quick to write, but mistakes show up only when that exact line runs.

### Static typing (optional)

If you write the type after the variable name with a colon, the variable is **locked** to that type:

```gdscript
extends Node


func _ready() -> void:
	var health: int = 100
	health = 90            # OK
	# health = "ninety"    # ERROR: caught immediately while typing
```

Benefits of static typing:

- **Errors are caught early,** often as you type, rather than in the middle of a game.
- **Better auto-completion** in the editor, since it knows what the variable is.
- **Better performance.** Typed code runs faster in many cases, because the engine can skip checks.
- **Clearer code.** The reader knows what each variable holds.

### Which should you use?

Both are valid. This course uses a **mix**: dynamic typing in small demonstration snippets for simplicity, and static typing in real project code. Many experienced developers prefer static typing everywhere. Static typing is covered in depth in [Lesson 17](./[17]-Static-Typing.md).

---

## 5.5 Basic Data Types Overview

Here are the most common built-in types. Each is covered in more detail in later lessons.

| Type | Description | Example |
| --- | --- | --- |
| `bool` | True or false | `true`, `false` |
| `int` | Whole number (64-bit) | `42`, `-7`, `0xFF`, `1_000_000` |
| `float` | Decimal number (64-bit) | `3.14`, `-0.5`, `1e3` |
| `String` | Text | `"Hello"`, `'Hi'` |
| `StringName` | Fast, unique text identifier | `&"jump"` |
| `NodePath` | A path to a node | `^"Player/Sprite2D"` |
| `Vector2` / `Vector3` | 2D / 3D coordinates | `Vector2(10, 20)` |
| `Color` | A color | `Color.RED`, `Color(1, 0, 0)` |
| `Array` | Ordered list | `[1, 2, 3]` |
| `Dictionary` | Key-value pairs | `{"hp": 10}` |
| `Callable` | A reference to a function | `print` |
| `Signal` | A signal reference | `button.pressed` |
| `Object` / `Node` / `Resource` | Engine objects | `Node.new()` |
| `null` | "Nothing" | `null` |

```gdscript
extends Node


func _ready() -> void:
	var is_jumping: bool = false
	var lives: int = 3
	var speed: float = 250.5
	var title: String = "Adventure"
	var position: Vector2 = Vector2(100, 50)
	var tint: Color = Color.CORNFLOWER_BLUE
	var items: Array = ["sword", "shield"]
	var stats: Dictionary = {"hp": 10, "mp": 5}

	print(is_jumping, lives, speed, title, position, tint, items, stats)
```

### Number literals

```gdscript
extends Node


func _ready() -> void:
	var decimal = 1000
	var with_separators = 1_000_000   # Underscores improve readability
	var hexadecimal = 0xFF            # 255
	var binary = 0b1010               # 10
	var scientific = 1.5e3            # 1500.0
	print(decimal, " ", with_separators, " ", hexadecimal, " ", binary, " ", scientific)
```

### `Variant`

Because dynamic variables can hold anything, Godot has a single umbrella type called **`Variant`**. A variable with no declared type is a `Variant`. See [Lesson 6](./[6]-Numbers-Strings-and-Booleans.md) and [Lesson 17](./[17]-Static-Typing.md).

---

## 5.6 Type Inference with `:=`

Writing out types by hand can get repetitive. The **inference operator** `:=` tells GDScript: *"work out the type from the value on the right, then lock the variable to it."*

```gdscript
extends Node


func _ready() -> void:
	var health := 100            # Inferred as int
	var speed := 250.5           # Inferred as float
	var title := "Adventure"     # Inferred as String
	var pos := Vector2(10, 20)   # Inferred as Vector2

	health = 90                  # OK
	# health = "dead"            # ERROR: health is now locked to int
	print(health, speed, title, pos)
```

Compare the three ways to declare a variable:

```gdscript
extends Node


func _ready() -> void:
	var a = 5        # Dynamic: no fixed type
	var b: int = 5   # Static: explicitly typed
	var c := 5       # Static: type inferred from the value
	print(a, b, c)
```

Here is a case where inference **cannot** work: when the type of the value is not known at load time.

```gdscript
extends Node


func _ready() -> void:
	var node = get_node("Sprite2D")      # OK: dynamic
	# var node2 := get_node("Sprite2D")  # ERROR: get_node() returns Node, which is
	#                                    # too general to infer a more specific type
	var node3: Sprite2D = get_node("Sprite2D")  # OK: explicit type
	print(node, node3)
```

Use `:=` when the right side has an obvious, specific type. Use an explicit `: Type` when you want a specific type that differs from the value's type, or when inference fails.

---

## 5.7 Type Checking with `typeof()` and `is`

Sometimes you need to ask, "What kind of value is this?" GDScript gives you two tools.

### `typeof()` returns a built-in type constant

```gdscript
extends Node


func _ready() -> void:
	var value = 42
	print(typeof(value))                  # 2  (the number TYPE_INT)
	print(typeof(value) == TYPE_INT)      # true
	print(typeof("hello") == TYPE_STRING) # true
	print(typeof(3.5) == TYPE_FLOAT)      # true
	print(typeof(null) == TYPE_NIL)       # true
```

Common constants are `TYPE_NIL`, `TYPE_BOOL`, `TYPE_INT`, `TYPE_FLOAT`, `TYPE_STRING`, `TYPE_VECTOR2`, `TYPE_COLOR`, `TYPE_ARRAY`, `TYPE_DICTIONARY`, and `TYPE_OBJECT`. To print a readable type name, use `type_string()`:

```gdscript
extends Node


func _ready() -> void:
	print(type_string(typeof(42)))      # int
	print(type_string(typeof("hi")))    # String
```

### `is` checks a value against a type or class

`is` returns `true` or `false`. It works for built-in types **and** for classes and nodes:

```gdscript
extends Node


func _ready() -> void:
	var number = 10
	var text = "hello"

	print(number is int)       # true
	print(text is String)      # true
	print(number is float)     # false

	var node: Node = Sprite2D.new()
	print(node is Sprite2D)    # true
	print(node is Node2D)      # true  (a Sprite2D IS a Node2D, through inheritance)
	print(node is Control)     # false
	node.free()
```

Use `typeof()` for built-in value types, and `is` for classes and for readable checks. `is not` is also available for the opposite check:

```gdscript
extends Node


func _ready() -> void:
	var value = "text"
	if value is not int:
		print("Not an integer")
```

---

## 5.8 Type Casting/Conversion (`as`, `int()`, `float()`, `str()`)

There are two different ideas here:

- **Casting** with `as` tells GDScript to treat an object as a more specific type.
- **Conversion** with `int()`, `float()`, `str()`, and similar functions creates a **new value** of a different type.

### Conversion functions

```gdscript
extends Node


func _ready() -> void:
	print(int(3.9))          # 3      (truncates toward zero, does not round)
	print(int("42"))         # 42
	print(float(5))          # 5.0
	print(float("3.14"))     # 3.14
	print(str(123))          # "123"
	print(str(true))         # "true"
	print(str(Vector2(1, 2)))  # "(1, 2)"
	print(bool(0))           # false
	print(bool(1))           # true
	print(bool(""))          # false
	print(bool("hi"))        # true
```

Notes:

- `int(3.9)` gives `3`, not `4`. To round, use `roundi(3.9)`, `round(3.9)`, `floori()`, or `ceili()`.
- Converting a non-numeric string with `int("abc")` returns `0`. If you need to check first, use `"abc".is_valid_int()`.
- You can convert **to** a string by joining with `str()` or a format string (see [Lesson 11](./[11]-String-Formatting.md)).

### The `as` operator

`as` casts a value to a class type. If the value is not that type, the result is `null`, and no error is raised:

```gdscript
extends Node


func _ready() -> void:
	var node: Node = Sprite2D.new()

	var sprite := node as Sprite2D     # Works: sprite is a Sprite2D
	print(sprite)

	var button := node as Button       # Not a Button: result is null
	print(button)                      # <null>

	node.free()
```

`as` is especially useful when a function returns a general type, and you know it is a specific one:

```gdscript
extends Node


func _ready() -> void:
	var sprite := get_node_or_null("Sprite2D") as Sprite2D
	if sprite:
		sprite.visible = false
```

For basic types, `as` can also do simple conversions, for example `var x = 3.7 as int`, but the conversion functions are clearer for that purpose.

---

## 5.9 Comments and Documentation Comments (`##`)

### Regular comments

Anything after a `#` on a line is ignored:

```gdscript
extends Node

# This whole line is a comment.
var speed = 200  # This is an end-of-line comment.


func _ready() -> void:
	# Comments explain WHY the code does something.
	pass
```

GDScript has **no multi-line comment syntax**, but you can comment out several lines at once: select them and press `Ctrl+K` (`Cmd+K` on macOS).

You can also use a multi-line string (`"""..."""`) as a block comment. Strictly, it is a string value that does nothing, but it is commonly used for notes.

### Special comment keywords

The editor highlights some words inside comments: `TODO`, `FIXME`, `HACK`, `NOTE`, `BUG`, and similar:

```gdscript
# TODO: add double jump
# FIXME: the player sinks into the floor on slopes
```

### Documentation comments (`##`)

A comment starting with **two** hash marks `##` is a **documentation comment**. Godot reads it and shows it in the built-in help and in tooltips:

```gdscript
## A simple health component.
##
## Attach this to any node that can take damage.
class_name HealthComponent
extends Node

## The maximum health this component can have.
@export var max_health: int = 100

## Emitted when health reaches zero.
signal died


## Reduces health by [param amount]. Returns [code]true[/code] if the target died.
func take_damage(amount: int) -> bool:
	max_health -= amount
	return max_health <= 0
```

Details:

- Place `##` **directly above** the class, variable, signal, constant, enum, or function it describes.
- Documentation comments support **BBCode-like tags** such as `[param name]`, `[code]text[/code]`, `[b]bold[/b]`, and `[Node]` for linking to a class.
- With `class_name`, your script appears in the Godot help search (`F1`) with your descriptions.

Good comments describe **why**, not **what**. `# Wait so the player cannot spam the button` helps more than `# Set timer to 0.5`.

---

[Previous](./[4]-Project-Structure-and-Settings.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[6]-Numbers-Strings-and-Booleans.md)