[Previous](./[14]-Dictionaries.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[16]-Built-in-Math-Types.md)

*Data Structures*

# Lesson 15 - Enums & Constants

Often a variable can only be one of a small, fixed set of choices: a character is `IDLE`, `RUNNING`, or `JUMPING`; a difficulty is `EASY`, `NORMAL`, or `HARD`. Storing those choices as plain numbers or strings is error-prone. **Enums** give each choice a readable name, and **constants** give fixed values a safe, central home. This lesson shows how to define and use both.

Unless noted otherwise, the examples assume they are placed in a script that starts with `extends Node`.

---

## 15.1 Defining Enums

An **enum** (short for *enumeration*) is a set of named integer constants. You define one with the `enum` keyword at the top level of a script:

```gdscript
extends Node

enum State { IDLE, RUN, JUMP, FALL }

var current_state: State = State.IDLE


func _ready() -> void:
	print(State.IDLE)       # 0
	print(State.RUN)        # 1
	print(State.JUMP)       # 2
	print(current_state)    # 0
```

Details:

- By default the first name is `0`, the next is `1`, and so on.
- Names are written in `CONSTANT_CASE`, and the enum name itself in `PascalCase`.
- You can assign **explicit values**. Later names continue counting from the last value:

```gdscript
extends Node

enum Difficulty { EASY = 1, NORMAL = 5, HARD = 10 }
enum Direction { UP = 10, DOWN, LEFT, RIGHT }     # 10, 11, 12, 13


func _ready() -> void:
	print(Difficulty.HARD)     # 10
	print(Direction.LEFT)      # 12
```

- Enums can be spread over several lines, with a trailing comma, which is good for version control:

```gdscript
extends Node

enum ItemType {
	WEAPON,
	ARMOR,
	POTION,
	KEY,
}
```

### Why not just use numbers or strings?

Compare these two versions:

```gdscript
extends Node


func bad() -> void:
	var state := 2                # What does 2 mean?
	if state == 2:
		print("Jumping?")


func good() -> void:
	var state := State.JUMP       # The code explains itself
	if state == State.JUMP:
		print("Jumping")

enum State { IDLE, RUN, JUMP }
```

Enums give you: readable code, auto-completion, protection from typos (a misspelled name is an error, whereas a misspelled string is not), and a single place to add a new option.

---

## 15.2 Named vs Anonymous Enums

### Named enums

A named enum creates a **new type**. Its values are accessed through the enum's name, and the name can be used as a type for variables and parameters:

```gdscript
extends Node

enum Team { RED, BLUE, GREEN }


func describe(team: Team) -> String:
	match team:
		Team.RED:
			return "Red team"
		Team.BLUE:
			return "Blue team"
	return "Other team"


func _ready() -> void:
	print(describe(Team.BLUE))                 # Blue team
	print(Team.keys())                         # ["RED", "BLUE", "GREEN"]
	print(Team.values())                       # [0, 1, 2]
	print(Team.find_key(1))                    # BLUE  (name from a value)
	print(Team["GREEN"])                       # 2     (value from a name)
```

A named enum behaves like a constant dictionary, so `keys()`, `values()`, and `find_key()` are available. This is handy for printing a readable name:

```gdscript
extends Node

enum State { IDLE, RUN, JUMP }

var state: State = State.RUN


func _ready() -> void:
	print("State is ", State.find_key(state))    # State is RUN
```

### Anonymous enums

If you leave out the name, the constants are placed **directly** in the script's scope:

```gdscript
extends Node

enum { LOW, MEDIUM, HIGH }


func _ready() -> void:
	print(MEDIUM)     # 1
```

Anonymous enums are handy for a few related constants you will not need as a type. Because the names are not grouped, they can clash with other names. Prefer **named** enums in most cases.

### Enums from other scripts

If a script has a `class_name`, its enums can be used from anywhere:

```gdscript
class_name Enemy
extends Node

enum Kind { SLIME, BAT, GOBLIN }

@export var kind: Kind = Kind.SLIME
```

From another script:

```gdscript
extends Node


func _ready() -> void:
	var k: Enemy.Kind = Enemy.Kind.BAT
	print(k)    # 1
```

### Type safety note

Even though an enum is made of integers, GDScript accepts any `int` for an enum-typed variable (with a possible warning). So an enum variable can technically hold a value that is not part of the enum. Handle the "none of the above" case in a `match`.

---

## 15.3 Using Enums with `match`

Enums and `match` ([Lesson 8](./[8]-Conditionals.md)) are a natural pair. A `match` on an enum reads like a table of behaviors:

```gdscript
extends Node

enum State { IDLE, RUN, JUMP, ATTACK }

var state: State = State.IDLE
var speed := 0.0


func set_state(new_state: State) -> void:
	state = new_state
	match state:
		State.IDLE:
			speed = 0.0
		State.RUN:
			speed = 200.0
		State.JUMP:
			speed = 150.0
		State.ATTACK:
			speed = 50.0
	print("Now ", State.find_key(state), " at speed ", speed)


func _ready() -> void:
	set_state(State.RUN)       # Now RUN at speed 200.0
	set_state(State.ATTACK)    # Now ATTACK at speed 50.0
```

Tips:

- When you `match` on an enum, add a branch for **every** value. If you add a new value later, the `match` is a good place to look for missing cases. You can add a final `_:` that reports the unexpected value with `push_warning()`.
- Multiple values can share a branch: `State.IDLE, State.ATTACK:`.
- Enums are also perfect as dictionary keys:

```gdscript
extends Node

enum Element { FIRE, WATER, EARTH }

const ELEMENT_NAMES := {
	Element.FIRE: "Fire",
	Element.WATER: "Water",
	Element.EARTH: "Earth",
}


func _ready() -> void:
	print(ELEMENT_NAMES[Element.WATER])    # Water
```

This pattern of an enum plus a `match` is the basis of the **state machine**, used for characters and enemies (see [Lesson 34](./[34]-Animation.md)).

---

## 15.4 Enums as Exported Dropdowns

When you combine an enum with `@export`, the Inspector shows a **dropdown menu** with the enum's names instead of a plain number box:

```gdscript
extends Node

enum Difficulty { EASY, NORMAL, HARD }
enum Weapon { SWORD, BOW, STAFF }

@export var difficulty: Difficulty = Difficulty.NORMAL
@export var starting_weapon: Weapon = Weapon.SWORD


func _ready() -> void:
	print("Difficulty: ", Difficulty.find_key(difficulty))
	print("Weapon: ", Weapon.find_key(starting_weapon))
```

In the Inspector you will see two dropdowns (*Difficulty* and *Starting Weapon*), and the choice you make is saved with the scene.

### Exporting a list of enum values

```gdscript
extends Node

enum Element { FIRE, WATER, EARTH, AIR }

@export var resistances: Array[Element] = []
```

The Inspector shows an array where each element has the dropdown.

### Alternative: `@export_enum`

For a quick dropdown without declaring an enum, `@export_enum` lists the options directly:

```gdscript
extends Node

@export_enum("Warrior", "Mage", "Rogue") var character_class: int = 0
@export_enum("Slow", "Normal", "Fast") var speed_name: String = "Normal"
```

With an `int` variable, the stored value is the index. With a `String`, the stored value is the selected text. A real `enum` is usually better because you can use the names in code. See [Lesson 27](./[27]-Annotations.md) for more about `@export`.

### Flags: enums as bit masks

`@export_flags` shows checkboxes, storing the choices as a bitmask (see bitwise operators in [Lesson 7](./[7]-Operators-and-Expressions.md)):

```gdscript
extends Node

@export_flags("Fire", "Water", "Earth", "Air") var immunities: int = 0


func _ready() -> void:
	var fire_bit := 1 << 0
	if immunities & fire_bit:
		print("Immune to fire")
```

---

## 15.5 Global Constants and `preload()` Constants

Constants (`const`) were introduced in [Lesson 5](./[5]-Variables-and-Data-Types.md). Here is how to use them well in larger projects.

### Script-level constants

```gdscript
class_name GameConfig
extends Node

const MAX_PLAYERS = 4
const START_LIVES = 3
const GRAVITY = 980.0
const PLAYER_COLORS = [Color.RED, Color.BLUE, Color.GREEN, Color.YELLOW]
```

Because the script has a `class_name`, other scripts can read these as `GameConfig.MAX_PLAYERS` **without** creating an instance:

```gdscript
extends Node


func _ready() -> void:
	print(GameConfig.MAX_PLAYERS)    # 4
	print(GameConfig.GRAVITY)        # 980.0
```

### A constants-only script

A common pattern is a script that only holds constants and enums:

```gdscript
class_name Constants
extends RefCounted

const TILE_SIZE = 16
const SAVE_PATH = "user://save.json"

enum Layer { WORLD = 1, PLAYER = 2, ENEMY = 4 }
```

Using `extends RefCounted` rather than `Node` makes it a light class that never needs to be in a scene.

### Constants without `class_name`: `preload()`

You can also load a script into a constant and use it through that name. `preload()` loads the resource when the script is loaded (at compile time), and the path must be a **constant string**:

```gdscript
extends Node

const Config = preload("res://config.gd")
const BULLET_SCENE = preload("res://scenes/bullet.tscn")
const HIT_SOUND = preload("res://audio/hit.wav")


func _ready() -> void:
	print(Config.MAX_PLAYERS)
	var bullet := BULLET_SCENE.instantiate()
	add_child(bullet)
```

Compare `preload()` and `load()`:

| | `preload("path")` | `load("path")` |
| --- | --- | --- |
| When it loads | When the script loads | When the line runs |
| Path | A constant string only | Any string, including computed ones |
| Missing file | Error when the script is loaded | `null` at runtime |
| Use for | Assets you always need | Assets chosen at runtime |

### Constants vs enums vs `@export`

| Use... | When the value is... |
| --- | --- |
| `const` | Fixed and never changes (tile size, maximum health) |
| `enum` | One of a fixed set of named choices |
| `@export var` | A tunable value you want to edit per node in the Inspector |
| `var` | Changing during the game |

### Rules of thumb

- Replace "magic numbers" in your code (`if level > 50`) with named constants (`if level > MAX_LEVEL`).
- Name constants `CONSTANT_CASE`. Name preloaded scenes and scripts in `PascalCase`, since they act like classes (`const BulletScene = preload(...)`).
- Group related constants together, at the top of the script.
- Remember that a `const` cannot be reassigned, and constant arrays and dictionaries cannot be modified. For data you do need to change, use a normal `var`.

---

[Previous](./[14]-Dictionaries.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[16]-Built-in-Math-Types.md)