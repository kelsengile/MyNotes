[Previous](./[19]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[21]-Nodes-and-the-Scene-Tree.md)

*Types and Classes*

# Lesson 20 - Properties, Setters and Getters

A **property** looks like an ordinary variable: you read it with `player.health` and change it with `player.health = 50`. The difference is that you can attach code that runs **whenever the value is read or written**. This lets a class keep its values valid (health never drops below zero), react to changes (update a health bar), or calculate values on demand (an area from a width and height). This lesson shows how to write setters and getters, where they are useful, and the traps to watch for.

Unless noted otherwise, the examples assume they are placed in a script that starts with `extends Node`.

---

## 20.1 Why Properties?

Imagine a player with health. With a plain variable, any code can store any value:

```gdscript
extends Node

var health: int = 100


func _ready() -> void:
	health = -50          # Nothing stops this. Health is now negative!
	health = 9999         # Or far above the maximum.
	print(health)
```

You could write a method like `set_health()` and ask everyone to use it, but nothing forces them to. With a **property**, the rule is attached to the variable itself, so it applies **every time**, from anywhere:

| Need | Property solution |
| --- | --- |
| Keep a value in a valid range | A **setter** that clamps it |
| Do something when a value changes | A **setter** that emits a signal or updates the screen |
| Calculate a value from other values | A **getter** with no stored data |
| Make a value read-only | A **getter** and no setter |

---

## 20.2 Setters and Getters

Add a colon after the variable declaration, then indent a `set` block, a `get` block, or both:

```gdscript
extends Node

var max_health: int = 100

var health: int = 100:
	set(value):
		health = clampi(value, 0, max_health)
	get:
		return health


func _ready() -> void:
	health = -50
	print(health)       # 0    (clamped up to the minimum)
	health = 9999
	print(health)       # 100  (clamped down to the maximum)
	health = 40
	print(health)       # 40
```

How it works:

- **`set(value):`** runs whenever the variable is assigned. `value` holds what the caller is trying to store. You decide what actually gets stored.
- **`get:`** runs whenever the variable is read. Its job is to `return` the value.
- **Inside its own setter or getter**, using the variable's name reaches the *stored* value directly. That is why `health = clampi(...)` inside `set` does not call the setter again (no endless loop).
- Both blocks are optional. If you only need a setter, leave out `get`: reading then simply returns the stored value.
- Indent with tabs, like all GDScript blocks.

A setter-only version is the most common form:

```gdscript
extends Node

var score: int = 0:
	set(value):
		score = maxi(value, 0)     # Score can never be negative


func _ready() -> void:
	score += 10      # Calls the setter with 10
	score -= 50      # Calls the setter with -40, which is stored as 0
	print(score)     # 0
```

Notice that **compound assignment** (`+=`, `-=`) also goes through the setter, because it reads the value, calculates, and writes the result back.

### Using separate functions

You can point to regular functions instead of writing the code inline. This is handy when the logic is long or shared:

```gdscript
extends Node

var max_health: int = 100
var health: int = 100:
	set = set_health,
	get = get_health


func set_health(value: int) -> void:
	health = clampi(value, 0, max_health)


func get_health() -> int:
	return health
```

Inside `set_health()`, assigning to `health` writes the stored value directly, exactly as it does in the inline form. Outside the class, `player.health = 5` calls `set_health(5)`.

### Setters and signals

A setter is the cleanest place to announce that something changed ([Lesson 26](./[26]-Signals.md)):

```gdscript
extends Node

signal health_changed(new_value: int)

var max_health: int = 100
var health: int = max_health:
	set(value):
		var clamped := clampi(value, 0, max_health)
		if clamped == health:
			return                       # Nothing changed, so stay quiet
		health = clamped
		health_changed.emit(health)


func _ready() -> void:
	health_changed.connect(func(new_value: int) -> void: print("Health is now ", new_value))
	health -= 30       # Health is now 70
	health -= 500      # Health is now 0
	health -= 10       # (no output: already 0, nothing changed)
```

A health bar can connect to `health_changed` and redraw itself, and the code that deals damage never needs to know the bar exists.

---

## 20.3 Computed and Read-Only Properties

A property does not have to store anything. If it has only a `get` block, the value is **calculated each time** it is read:

```gdscript
extends Node

var width: float = 4.0
var height: float = 3.0

var area: float:
	get:
		return width * height


func _ready() -> void:
	print(area)        # 12.0
	width = 10.0
	print(area)        # 30.0   (always up to date, nothing to keep in sync)
	# area = 5.0       # ERROR: area has no setter, so it is read-only
```

A computed property avoids a classic bug: two variables that are supposed to agree but get out of step. Here `area` cannot be wrong, because it is never stored.

### A computed property with a setter

You can also write to a computed property by translating the value back into the real data:

```gdscript
extends Node

var radius: float = 5.0

var diameter: float:
	get:
		return radius * 2.0
	set(value):
		radius = value / 2.0


func _ready() -> void:
	print(diameter)    # 10.0
	diameter = 30.0
	print(radius)      # 15.0
```

Keep getters **fast and free of side effects**. A getter that loads a file or changes other values will surprise whoever reads `player.something` and expects a quick lookup. If the work is heavy, use a method with a clear action name instead (`calculate_path()`).

### Backing variables

When the stored value and the public property need different names (or you want to hide the real storage), use a **backing variable**. By convention it starts with an underscore:

```gdscript
extends Node

var _health: int = 10          # "Private" storage
var health: int:
	get:
		return _health
	set(value):
		_health = maxi(value, 0)


func _ready() -> void:
	health = -3
	print(health)     # 0
```

Other code uses `health`. The underscore signals that `_health` is an internal detail (GDScript does not enforce this, see [Lesson 18](./[18]-Classes-and-Scripts.md)).

---

## 20.4 Properties, `@export`, and the Inspector

Properties combine well with `@export` ([Lesson 27](./[27]-Annotations.md)). The setter runs when the value is changed in the Inspector, so the scene can update immediately.

The following script redraws a circle as soon as you edit its radius. `@tool` ([Lesson 3](./[3]-Running-GDScript-Code.md)) makes it run in the editor so you see the effect without pressing play:

```gdscript
@tool
extends Node2D

@export var radius: float = 20.0:
	set(value):
		radius = maxf(value, 1.0)
		queue_redraw()                # Ask Godot to call _draw() again


func _draw() -> void:
	draw_circle(Vector2.ZERO, radius, Color.CORNFLOWER_BLUE)
```

### The timing trap

When a scene loads, exported values are applied **before** the node is in the tree and **before** `_ready()` runs. A setter that touches child nodes may therefore run while those children do not exist yet, causing a `null` error (see [Lesson 12](./[12]-Error-Handling.md)).

Guard against this with `is_node_ready()`, and apply the value again in `_ready()`:

```gdscript
extends Node

@export var title: String = "Untitled":
	set(value):
		title = value
		if is_node_ready():           # Only touch children once they exist
			$TitleLabel.text = value


func _ready() -> void:
	$TitleLabel.text = title          # Apply the value that was loaded with the scene
```

---

## 20.5 Common Pitfalls

### 1. The starting value does not call the setter

The default value in the declaration is stored **directly**, without running the setter:

```gdscript
extends Node

var health: int = 500:
	set(value):
		health = clampi(value, 0, 100)


func _ready() -> void:
	print(health)       # 500  (the initial value skipped the clamp!)
	health = 500
	print(health)       # 100  (an assignment, so now the setter runs)
```

Start with a value that is already valid, or assign it again in `_init()` or `_ready()`.

### 2. Changing the contents of an array or dictionary does not call the setter

A setter fires on **assignment**. Changing what is *inside* a collection is not an assignment:

```gdscript
extends Node

var items: Array[String] = []:
	set(value):
		items = value
		print("Items changed!")


func _ready() -> void:
	items = ["sword"]          # Prints "Items changed!"
	items.append("shield")     # Prints nothing: the setter was not called
```

If listeners must know about every change, add methods such as `add_item()` that change the array and emit a signal ([Lesson 13](./[13]-Arrays.md)).

### 3. Expensive or surprising getters

Because a getter looks like a variable, people call it freely, perhaps inside a loop. Keep it cheap.

### 4. Forgetting the type

For a property that has no stored default (only `get`), you must declare its type: `var area: float:`. Without a type and without a value, GDScript has nothing to infer ([Lesson 17](./[17]-Static-Typing.md)).

---

## 20.6 Reading and Writing Properties by Name

Every property can also be accessed with a **string name**, using `get()` and `set()`. This is useful for generic code, such as tweaking whichever property the caller names:

```gdscript
extends Node

var speed: int = 5


func _ready() -> void:
	print(get("speed"))       # 5
	set("speed", 12)
	print(speed)              # 12
```

These work on any object and run the property's setter or getter if one exists:

```gdscript
extends Node


func _ready() -> void:
	var sprite := Sprite2D.new()
	sprite.set("position", Vector2(10, 20))
	print(sprite.get("position"))     # (10, 20)
	sprite.free()
```

### Advanced: `_get()` and `_set()`

For fully dynamic objects, a class can define the special methods `_get(property)` and `_set(property, value)`. Godot calls them when a property is requested that the class does not declare itself. Return `null` from `_get` (or `false` from `_set`) when you do not handle the name:

```gdscript
# stat_block.gd
class_name StatBlock
extends RefCounted

var _stats: Dictionary = {"strength": 5, "speed": 3}


func _get(property: StringName) -> Variant:
	var key := String(property)
	if _stats.has(key):
		return _stats[key]
	return null


func _set(property: StringName, value: Variant) -> bool:
	var key := String(property)
	if _stats.has(key):
		_stats[key] = value
		return true          # We handled it
	return false             # Not ours, let Godot handle it
```

```gdscript
extends Node


func _ready() -> void:
	var stats := StatBlock.new()
	print(stats.get("strength"))    # 5
	stats.set("speed", 9)
	print(stats.get("speed"))       # 9
```

This is an advanced tool. For almost every case, ordinary properties with `set` and `get` blocks are clearer and safer.

### Summary

| You want... | Write |
| --- | --- |
| A plain value | `var score: int = 0` |
| A value kept in range | `var health: int = 100:` with a `set(value):` block |
| A change notification | A setter that calls `signal_name.emit(...)` |
| A calculated value | `var area: float:` with only a `get:` block |
| A read-only value | Same as above (no setter) |
| An editable value that updates the scene | `@export var` with a setter, guarded with `is_node_ready()` |

---

[Previous](./[19]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[21]-Nodes-and-the-Scene-Tree.md)