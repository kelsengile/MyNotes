[Previous](./[17]-Static-Typing.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[19]-Inheritance-and-Polymorphism.md)

*Types and Classes*

# Lesson 18 - Classes and Scripts

So far you have used classes without calling them that: `Vector2`, `Node`, and `Array` are all classes. A **class** is a blueprint that describes what data something holds (its **variables**) and what it can do (its **functions**). An **object** (or **instance**) is one actual thing built from that blueprint. In GDScript, every script file is a class. This lesson shows how to define your own classes, create objects from them, give them constructors, share data with static members, and organize a script cleanly.

Because this lesson uses several scripts at once, the first line of each example is a comment naming the file it belongs to.

---

## 18.1 Every Script Is a Class

A `.gd` file **is** a class definition. When you attach a script to a node, the node becomes an instance of that class. The pieces you already know map directly onto class vocabulary:

| GDScript | Class term |
| --- | --- |
| Variable at the top of the script | **Member variable** (or property) |
| Function in the script | **Method** |
| `extends Something` | The **parent class** (what this class builds upon) |
| A node using the script, or `.new()` | An **instance** (object) |

```gdscript
# counter.gd
extends Node

var count: int = 0                 # Member variable


func increment() -> void:          # Method
	count += 1
	print("Count is ", count)
```

If you attach `counter.gd` to two different nodes, you get **two separate instances**, each with its own `count`.

### `extends`

`extends` names the class your script builds upon. It gives your class everything the parent has:

```gdscript
extends Node2D       # Has position, rotation, scale, add_child(), queue_free(), ...
```

If you leave `extends` out, the script extends **`RefCounted`**, a lightweight class that is perfect for plain data and logic objects that are not nodes.

### Choosing what to extend

| Extend | Use it for | Memory handling |
| --- | --- | --- |
| `RefCounted` (the default) | Plain logic or data objects (inventory, pathfinder, parser) | Automatic |
| `Resource` | Data you want to save, load, and edit in the Inspector ([Lesson 23](./[23]-Custom-Resources.md)) | Automatic |
| `Node` and its children | Anything that lives in the scene tree | Freed with `queue_free()` |
| `Object` | Rare, low-level cases | Manual with `free()` |

The next lesson ([Lesson 19](./[19]-Inheritance-and-Polymorphism.md)) covers `extends` in depth.

---

## 18.2 Naming a Class with `class_name`

By default a script is only reachable by its file path. Adding `class_name` registers the script **globally** under a name you choose. After that you can use the name as a type or to create objects from anywhere, with no `preload()` needed.

```gdscript
# enemy.gd
class_name Enemy
extends RefCounted

var enemy_name: String = "Slime"
var hp: int = 10


func take_damage(amount: int) -> void:
	hp = maxi(hp - amount, 0)
	print("%s has %d hp left" % [enemy_name, hp])
```

Now any other script can use it:

```gdscript
# main.gd
extends Node


func _ready() -> void:
	var slime: Enemy = Enemy.new()    # Enemy is now both a type and a constructor
	slime.take_damage(4)              # Slime has 6 hp left
```

Things to know about `class_name`:

- It must be on the **first line or near the top**, before `extends` (annotations such as `@tool` and `@icon` come before it).
- Class names must be **unique** in the project and must not match an engine class (`Node`, `Sprite2D`) or an autoload name.
- Use `PascalCase`. The file name is usually the `snake_case` version (`enemy.gd` for `Enemy`).
- If the script extends `Node`, the class also appears in the **Create New Node** dialog, so you can add it to a scene directly.
- You can give the class a custom icon with `@icon("res://icons/enemy.svg")` placed above `class_name`.
- You can write both on one line: `class_name Enemy extends RefCounted`.

A `class_name` is the reason you can write `var target: Enemy` in static typing ([Lesson 17](./[17]-Static-Typing.md)), and `if body is Enemy:` in a collision check.

---

## 18.3 Creating and Using Instances

### `.new()`

Call `.new()` on a class to build an instance:

```gdscript
extends Node


func _ready() -> void:
	var a := Enemy.new()
	var b := Enemy.new()

	a.enemy_name = "Bat"
	a.hp = 6

	a.take_damage(2)    # Bat has 4 hp left
	b.take_damage(2)    # Slime has 8 hp left (b is separate from a)
```

Each instance has its **own** copy of the member variables. Changing `a` never changes `b`.

### `self`

Inside a method, `self` means "this instance". It is usually optional, but it helps when a parameter has the same name as a member:

```gdscript
# enemy.gd (excerpt)
func heal(hp: int) -> void:     # Warning: this parameter shadows the member "hp"
	self.hp += hp               # self.hp is the member, hp is the parameter
```

Prefer different names (`amount`) so you do not need this.

### Freeing objects

How an object is cleaned up depends on what it extends:

```gdscript
extends Node


func _ready() -> void:
	# RefCounted: freed automatically when nothing refers to it any more
	var data := Enemy.new()
	print(data.hp)

	# Node: NOT freed automatically. You must free it.
	var temp := Node.new()
	temp.free()                 # Immediately delete a node that is not in the tree

	var child := Node.new()
	add_child(child)            # Now it is in the scene tree...
	child.queue_free()          # ...so ask the engine to delete it safely at the end of the frame
```

Forgetting to free a node you created with `.new()` is a **memory leak**. If a node is inside the scene tree, use `queue_free()`. See [Lesson 12](./[12]-Error-Handling.md) for `is_instance_valid()` when you keep references to nodes that may be freed.

---

## 18.4 Constructors with `_init()`

The special method **`_init()`** is the **constructor**. It runs automatically when an object is created, so it is the place to set up starting values.

```gdscript
# weapon.gd
class_name Weapon
extends RefCounted

var weapon_name: String
var damage: int


func _init(new_name: String = "Stick", new_damage: int = 1) -> void:
	weapon_name = new_name
	damage = new_damage


func describe() -> String:
	return "%s (damage %d)" % [weapon_name, damage]
```

The arguments you pass to `.new()` go straight to `_init()`:

```gdscript
extends Node


func _ready() -> void:
	var stick := Weapon.new()                   # Uses both defaults
	var sword := Weapon.new("Sword", 12)
	print(stick.describe())                     # Stick (damage 1)
	print(sword.describe())                     # Sword (damage 12)
```

### Important rules

- `_init()` has no return type other than `void`. You do not `return` the object. `.new()` does that.
- **Give every parameter a default if the script will be attached to a node.** When Godot builds a node from a scene, it calls `_init()` with **no arguments**. A required parameter would cause an error.
- `_init()` runs **before** the node is in the scene tree and before `@export` values from the scene are applied. For anything that needs the tree or the Inspector values, use `_ready()` ([Lesson 25](./[25]-Lifecycle-and-Callbacks.md)).
- If the parent class has an `_init()` that requires arguments, your `_init()` must pass them with `super(...)` ([Lesson 19](./[19]-Inheritance-and-Polymorphism.md)).

### Factory functions

When creation needs more logic than a constructor comfortably holds (loading from a dictionary, for example), write a **static factory function** (static functions are covered next):

```gdscript
# enemy.gd (excerpt)
static func from_dictionary(data: Dictionary) -> Enemy:
	var enemy := Enemy.new()
	enemy.enemy_name = data.get("name", "Unnamed")
	enemy.hp = data.get("hp", 10)
	return enemy
```

```gdscript
extends Node


func _ready() -> void:
	var goblin := Enemy.from_dictionary({"name": "Goblin", "hp": 15})
	goblin.take_damage(5)    # Goblin has 10 hp left
```

---

## 18.5 Static Variables and Static Functions

Normally data belongs to an **instance**. A **static** member belongs to the **class itself** and is shared by every instance. You call it through the class name, with no instance needed.

### Static functions

```gdscript
# math_tools.gd
class_name MathTools
extends RefCounted


static func average(a: float, b: float) -> float:
	return (a + b) / 2.0


static func is_even(n: int) -> bool:
	return n % 2 == 0
```

```gdscript
extends Node


func _ready() -> void:
	print(MathTools.average(4.0, 9.0))   # 6.5
	print(MathTools.is_even(7))          # false
```

A static function cannot use `self` or any non-static member variable, because it does not belong to an instance. It can only use its parameters, constants, and other static members. This makes static functions ideal for **utility** helpers.

### Static variables

`static var` (Godot 4.1 and later) stores one value shared by the whole class:

```gdscript
# id_generator.gd
class_name IdGenerator
extends RefCounted

static var _next_id: int = 1


static func next_id() -> int:
	var id := _next_id
	_next_id += 1
	return id
```

```gdscript
extends Node


func _ready() -> void:
	print(IdGenerator.next_id())   # 1
	print(IdGenerator.next_id())   # 2
	print(IdGenerator.next_id())   # 3
```

Every call to `next_id()` anywhere in the game sees the same counter.

### Static initialization

`_static_init()` is a special static function that runs **once**, when the class is first loaded. Use it to prepare static variables that need more than a simple value:

```gdscript
# damage_table.gd
class_name DamageTable
extends RefCounted

static var values: Dictionary = {}


static func _static_init() -> void:
	values["dagger"] = 4
	values["sword"] = 8
```

### Constants are shared too

A `const` is always shared by the class and is read through the class name (`GameConfig.MAX_PLAYERS`, [Lesson 15](./[15]-Enums-and-Constants.md)).

| Kind | Belongs to | Accessed through |
| --- | --- | --- |
| `var` / `func` | One instance | The instance (`slime.hp`) |
| `static var` / `static func` | The class | The class name (`IdGenerator.next_id()`) |
| `const` / `enum` | The class | The class name (`GameConfig.MAX_PLAYERS`) |

> **Note:** Static variables live for the whole run of the game and are shared everywhere, so treat them like global state. Use them sparingly. Autoloads ([Lesson 30](./[30]-Groups-and-Autoloads.md)) are usually the better home for game-wide data such as score.

---

## 18.6 Inner Classes

A script can contain **inner classes**: small classes defined inside the file with the `class` keyword. They are useful for helper data that no other script needs.

```gdscript
# inventory.gd
extends Node


class Item:
	var item_name: String
	var value: int

	func _init(new_name: String, new_value: int) -> void:
		item_name = new_name
		value = new_value

	func describe() -> String:
		return "%s worth %d gold" % [item_name, value]


var items: Array[Item] = []


func _ready() -> void:
	items.append(Item.new("Ruby", 200))
	items.append(Item.new("Coin", 1))
	for item in items:
		print(item.describe())
```

Output:

```
Ruby worth 200 gold
Coin worth 1 gold
```

Facts about inner classes:

- They extend `RefCounted` unless you write `extends` inside them (for example `class Foo extends Node:`).
- They can be used as types (`Array[Item]`) inside the same script.
- If the outer script has a `class_name`, other scripts can reach an inner class through it: `Inventory.Item.new("Gem", 50)`.
- They cannot be attached to nodes or saved as their own file. When a class grows large or needs to be shared, move it into its own `.gd` file with a `class_name`.

---

## 18.7 Scripts as Resources: `preload()` and `load()`

A script file is also a **resource** that can be loaded into a variable or constant. This is how you use a class that has **no** `class_name`:

```gdscript
extends Node

const Weapon = preload("res://weapon.gd")      # PascalCase: it acts like a class


func _ready() -> void:
	var sword := Weapon.new("Sword", 12)
	print(sword.describe())
```

`preload()` loads when the script is loaded and needs a constant path. `load()` loads when the line runs and accepts any string ([Lesson 15](./[15]-Enums-and-Constants.md) compares them).

Other useful tools:

```gdscript
extends Node


func _ready() -> void:
	var slime := Enemy.new()
	print(slime is Enemy)               # true
	print(slime.get_script())           # The Enemy script resource
	print(slime.get_class())            # RefCounted (the built-in class it ultimately extends)
	print(slime.is_class("RefCounted")) # true
```

`get_class()` always returns the **native engine class**, never your `class_name`. Use `is Enemy` to test for your own classes.

---

## 18.8 Organizing a Script

The official style guide suggests keeping the parts of a script in a consistent order, so you always know where to look. Here is a simplified version of that order:

1. `@tool`, `@icon`
2. `class_name`
3. `extends`
4. A `##` documentation comment describing the class
5. Signals ([Lesson 26](./[26]-Signals.md))
6. Enums
7. Constants
8. Static variables
9. `@export` variables ([Lesson 27](./[27]-Annotations.md))
10. Regular variables
11. `@onready` variables
12. `_init()` and other built-in virtual methods in this order: `_init`, `_ready`, `_process`, `_physics_process`
13. Your own public methods
14. Private methods (names starting with `_`)
15. Inner classes

A complete example:

```gdscript
# player_stats.gd
class_name PlayerStats
extends RefCounted
## Tracks a player's health and level.

signal died

enum Rank { ROOKIE, VETERAN }

const MAX_HEALTH: int = 100

static var total_created: int = 0

var health: int = MAX_HEALTH
var level: int = 1


func _init() -> void:
	total_created += 1


func take_damage(amount: int) -> void:
	health = maxi(health - amount, 0)
	if health == 0:
		died.emit()


func rank() -> Rank:
	return _compute_rank()


func _compute_rank() -> Rank:
	return Rank.VETERAN if level >= 10 else Rank.ROOKIE
```

Style notes:

- A **`##` comment** directly above a declaration becomes documentation shown in the editor's built-in help ([Lesson 2](./[2]-Editor-Tour.md)).
- GDScript has **no `private` keyword**. By convention, a leading underscore (`_compute_rank`, `_next_id`) means "internal, please do not use from outside". The language does not stop you, but other programmers will respect it.
- Keep each class focused on **one job**. A class named `Player` that also handles saving, UI, and sound is a sign it should be split.

---

[Previous](./[17]-Static-Typing.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[19]-Inheritance-and-Polymorphism.md)