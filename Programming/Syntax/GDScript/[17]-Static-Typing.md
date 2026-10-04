[Previous](./[16]-Built-in-Math-Types.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[18]-Classes-and-Scripts.md)

*Types and Classes*

# Lesson 17 - Static Typing

GDScript is a **dynamically typed** language by default: a variable can hold any kind of value, and mistakes only show up when the game runs. GDScript also supports **optional static typing**. When you tell the engine what type a variable, parameter, or return value should be, it can find many mistakes *while you type*, give better auto-completion, and run your code faster. This lesson explains how to add types, when the engine can work them out for you, and how to turn on warnings that keep your code safe.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 17.1 Dynamic vs Static Typing

In a **dynamically typed** variable, the type belongs to the *value*, not to the variable. The same variable can hold a number now and a string later:

```gdscript
extends Node


func _ready() -> void:
	var thing = 10          # No type given: thing holds an int
	thing = "ten"           # Fine: now it holds a String
	thing = Vector2(1, 2)   # Still fine
	print(thing)            # (1, 2)
```

In a **statically typed** variable, the type belongs to the *variable* and cannot change:

```gdscript
extends Node


func _ready() -> void:
	var health: int = 10
	# health = "ten"        # ERROR: Cannot assign a value of type String to variable "health" with specified type int.
	health = 20             # OK
	print(health)
```

The two styles can be mixed freely in one script. Godot calls the "anything goes" type **`Variant`**. Every untyped variable is secretly a `Variant`, and you can also write it out on purpose:

```gdscript
extends Node


func _ready() -> void:
	var anything: Variant = 5
	anything = "now a string"
	print(anything)
```

| | Dynamic (untyped) | Static (typed) |
| --- | --- | --- |
| Variable can change type | Yes | No |
| Mistakes found | When the line runs | In the editor, before running |
| Auto-completion | Limited | Full |
| Speed | Normal | Usually faster |
| Good for | Quick experiments | Anything you plan to keep |

---

## 17.2 Typed Variables

### Explicit types

Write a colon and the type after the variable name:

```gdscript
extends Node


func _ready() -> void:
	var health: int = 100
	var speed: float = 200.0
	var title: String = "Hero"
	var alive: bool = true
	var position: Vector2 = Vector2(10, 20)
	var color: Color = Color.RED
	var target: Node = null
	print(health, " ", speed, " ", title, " ", alive, " ", position, " ", color, " ", target)
```

You can declare a typed variable without a value. It receives that type's default (`0` for `int`, `""` for `String`, `null` for objects):

```gdscript
extends Node


func _ready() -> void:
	var count: int
	var label: String
	print(count)    # 0
	print(label)    # (empty line)
```

Any type you have met so far can be used: the basic types (`int`, `float`, `String`, `bool`), math types such as `Vector2` and `Color`, engine classes such as `Node`, `Sprite2D`, and `Resource`, your own `class_name` classes ([Lesson 18](./[18]-Classes-and-Scripts.md)), and enums ([Lesson 15](./[15]-Enums-and-Constants.md)).

Typed variables are checked when you assign to them, and also while the game runs. A wrong value is rejected either way:

```gdscript
extends Node


func _ready() -> void:
	var lives: int = 3
	lives = lives - 1       # OK: int minus int is an int
	# lives = "two"         # ERROR
	# lives = null          # ERROR: null cannot go in an int
```

Only **object** types (such as `Node`) may hold `null`. A plain `int`, `float`, `bool`, or `String` always holds a real value.

### Inferred types with `:=`

Typing every variable by hand is tiring. If you write `:=` instead of `=`, GDScript works out the type from the value on the right and locks it in:

```gdscript
extends Node


func _ready() -> void:
	var health := 100            # int
	var ratio := 0.5             # float
	var name_text := "Ada"       # String
	var spawn := Vector2(4, 8)   # Vector2
	var enemies := []            # Array

	# health = "full"            # ERROR: health is an int now
	print(health, ratio, name_text, spawn, enemies)
```

`var health := 100` and `var health: int = 100` mean exactly the same thing.

### When inference fails

The `:=` shortcut only works when the engine can be **sure** of the type. If the right side could be *anything* (a `Variant`), inference fails. This happens often with dictionary lookups and with elements taken from untyped arrays:

```gdscript
extends Node


func _ready() -> void:
	var data := {"hp": 10}

	# var hp := data["hp"]       # ERROR: Cannot infer the type of "hp" because the value doesn't have a set type.
	var hp: int = data["hp"]     # OK: you state the type yourself
	print(hp)
```

The rule of thumb: use `:=` when the type is obvious from the right side (`Vector2(1, 2)`, `"text"`, `5`), and write the type out when it is not.

### Constants and numbers

Constants can be typed or inferred too:

```gdscript
extends Node

const MAX_HEALTH: int = 100
const GRAVITY := 980.0


func _ready() -> void:
	print(MAX_HEALTH, " ", GRAVITY)
```

Types and numbers interact in two useful ways:

```gdscript
extends Node


func _ready() -> void:
	var a: float = 5       # OK: an int is automatically widened to a float (5.0)
	var b: int = 5.7       # Allowed, but warns about a "narrowing conversion"; the value becomes 5
	print(a, " ", b)       # 5.0 5
```

Going from `int` to `float` is always safe. Going from `float` to `int` throws away the decimals, so GDScript warns you. Be explicit with `int(5.7)` or `roundi(5.7)` when you really want this.

---

## 17.3 Typed Functions, Arrays, and Dictionaries

### Function parameters and return types

Functions were covered in [Lesson 10](./[10]-Functions-and-Scope.md). Types go after each parameter, and after `->` for the return value:

```gdscript
extends Node


func add(a: int, b: int) -> int:
	return a + b


func greet(player_name: String) -> void:
	print("Hello, ", player_name)


func _ready() -> void:
	print(add(2, 3))      # 5
	greet("Ada")
	# add("2", 3)         # ERROR: Invalid argument for "add()" function: argument 1 should be "int" but is "String".
	# var text: String = add(2, 3)   # ERROR: an int cannot go into a String
```

A function with a return type **must** return a value on every path. A `-> void` function must not return one.

Typed parameters can still have default values:

```gdscript
extends Node


func heal(amount: int = 10) -> int:
	return amount


func _ready() -> void:
	print(heal())      # 10
	print(heal(25))    # 25
```

### Typed arrays

`Array[Type]` limits what an array may hold (see [Lesson 13](./[13]-Arrays.md)):

```gdscript
extends Node


func _ready() -> void:
	var scores: Array[int] = [10, 20, 30]
	scores.append(40)
	# scores.append("fifty")     # ERROR

	var total := 0
	for score in scores:         # The editor knows score is an int
		total += score
	print(total)                 # 100
```

### Typed dictionaries

From **Godot 4.4**, dictionaries can type their keys and values (see [Lesson 14](./[14]-Dictionaries.md)):

```gdscript
extends Node


func _ready() -> void:
	var prices: Dictionary[String, int] = {"sword": 100, "potion": 15}
	prices["shield"] = 80
	var sword_price := prices["sword"]   # Inference works here: the value type is known to be int
	print(sword_price)                   # 100
```

Notice that `:=` works on the last line. With a typed dictionary the engine knows what a lookup returns, so there is nothing left to guess.

### Typed `for` loop variables

From **Godot 4.2** you can give the loop variable a type ([Lesson 9](./[9]-Loops.md)). This is useful when looping over an *untyped* array:

```gdscript
extends Node


func _ready() -> void:
	var mixed := [1, 2, 3]
	for number: int in mixed:
		print(number * 2)
```

### Typing with classes and enums

Object types work the same way. A variable typed as `Node2D` accepts any `Node2D` or anything that extends it (such as `Sprite2D`):

```gdscript
extends Node

enum State { IDLE, RUN }

var current_state: State = State.IDLE
var target: Node2D = null


func _ready() -> void:
	target = Sprite2D.new()      # OK: a Sprite2D is a Node2D
	print(target is Sprite2D)    # true
	target.free()
```

---

## 17.4 Type Checks and Casting (`is`, `as`)

Sometimes you receive a value typed too broadly. A signal might hand you a `Node2D` when you know it is a `CharacterBody2D`. Two operators help (they were introduced in [Lesson 7](./[7]-Operators-and-Expressions.md)).

### `is` checks the type

```gdscript
extends Node


func _ready() -> void:
	var node: Node = Node2D.new()
	print(node is Node2D)         # true
	print(node is Sprite2D)       # false
	print(node is not Sprite2D)   # true
	node.free()
```

### `as` casts to a type

`as` gives you the same object seen as a more specific type. If the object is **not** of that type, the result is `null` instead of an error:

```gdscript
extends Node


func _ready() -> void:
	var node: Node = Node2D.new()

	var as_2d := node as Node2D        # A Node2D
	var as_sprite := node as Sprite2D  # null: it is not a Sprite2D
	print(as_2d, " ", as_sprite)

	node.free()
```

The usual pattern is to cast, check for `null`, then use the result:

```gdscript
extends Node


func _on_body_entered(body: Node2D) -> void:
	var character := body as CharacterBody2D
	if character == null:
		return
	print("Character velocity: ", character.velocity)   # The editor now knows .velocity exists
```

### Converting values (not objects)

For numbers, strings and other built-in values, use the conversion functions instead of `as`:

```gdscript
extends Node


func _ready() -> void:
	print(int("42"))        # 42
	print(int(3.9))         # 3     (truncates)
	print(float("2.5"))     # 2.5
	print(str(100))         # 100
	print(String.num(3.14159, 2))   # 3.14
```

Check text from players with `is_valid_int()` first ([Lesson 12](./[12]-Error-Handling.md)), because `int("abc")` quietly gives `0`.

---

## 17.5 Why Use Static Typing?

Typing is optional, so why bother?

1. **Errors appear immediately.** A typo or wrong value is underlined in red as you type, rather than crashing the game minutes later.
2. **Better auto-completion.** When the editor knows a variable is a `Sprite2D`, it can list `texture`, `flip_h`, and every other member.
3. **Code documents itself.** `func damage(target: Enemy, amount: int) -> bool` explains its own use without comments.
4. **Speed.** The engine can use faster instructions when it knows the types. The gain depends on the code, but heavy loops (movement for many enemies, grid searches) can benefit noticeably.
5. **Safer refactoring.** When you rename or change something, the editor shows you every place that no longer fits.

### Safe and unsafe lines

The code editor can show you which lines are **type safe**. With **Highlight Type Safe Lines** enabled in **Editor > Editor Settings > Text Editor > Appearance > Gutters**, the line numbers of fully typed lines are drawn in a different color from lines that depend on untyped values. Those other lines are called **unsafe lines**. They still run, but the engine can only check them while the game runs.

Compare these two ways of reaching a node:

```gdscript
extends Node


func unsafe_version() -> void:
	var label = get_node("Label")     # Untyped: label is a Variant
	label.text = "Hi"                 # Unsafe: the editor cannot check that .text exists


func safe_version() -> void:
	var label := get_node("Label") as Label   # Typed: label is a Label (or null)
	if label != null:
		label.text = "Hi"             # Safe: the editor knows Label has .text
```

### Should everything be typed?

The official style guide encourages typing, but you do not have to type everything. A sensible approach for this course:

- **Always** type function parameters and return values.
- **Always** type member variables (those at the top of the script).
- Use `:=` for local variables when the type is obvious.
- Leave a value as `Variant` only when it genuinely can be anything, and say so with `: Variant`.
- Be **consistent** within a file. A half-typed script is harder to read than a fully typed or fully untyped one.

---

## 17.6 Typing Warnings and Project Settings

GDScript can warn you about code that is not typed or is unsafe. These warnings are configured in **Project > Project Settings > Debug > GDScript** (turn on **Advanced Settings** to see everything). Each warning can be set to **Ignore**, **Warn**, or **Error**.

| Warning | Triggers when |
| --- | --- |
| `UNTYPED_DECLARATION` | A variable, constant, parameter, or return type has no type at all |
| `INFERRED_DECLARATION` | A variable uses `:=` instead of an explicit type |
| `UNSAFE_PROPERTY_ACCESS` | You read or write a property on a value whose type is not known |
| `UNSAFE_METHOD_ACCESS` | You call a method on a value whose type is not known |
| `UNSAFE_CAST` | You cast a `Variant` to a concrete type with `as` |
| `UNSAFE_CALL_ARGUMENT` | You pass a value whose type is not known to a typed parameter |
| `NARROWING_CONVERSION` | A `float` is stored in an `int` and loses its decimals |
| `INTEGER_DIVISION` | Two integers are divided, discarding the remainder |

Most of the "unsafe" and "untyped" warnings are **ignored by default**, so beginners are not flooded with yellow. A good path is:

1. Start with the defaults.
2. Once you are comfortable, set `UNTYPED_DECLARATION` to **Warn**. The editor now points out everything you have not typed.
3. For strict projects, change selected warnings to **Error** so unsafe code cannot run.

### Silencing a single warning

If a warning is expected on one line, you can silence it with the `@warning_ignore` annotation placed directly above that statement:

```gdscript
extends Node


func _ready() -> void:
	@warning_ignore("integer_division")
	var half := 7 / 2          # 3, and we meant it
	print(half)
```

Use this sparingly and only when you are sure the code is correct. Silencing a warning is a decision, not a cleanup.

### A fully typed example

Putting the lesson together, here is a small script in which every declaration is typed:

```gdscript
extends Node

const MAX_HEALTH: int = 100

var health: int = MAX_HEALTH
var inventory: Array[String] = []


func take_damage(amount: int) -> bool:
	health = maxi(health - amount, 0)
	return health == 0


func pick_up(item: String) -> void:
	inventory.append(item)


func _ready() -> void:
	pick_up("potion")
	var dead: bool = take_damage(30)
	print("Health: ", health, ", dead: ", dead, ", items: ", inventory)
```

Output:

```
Health: 70, dead: false, items: ["potion"]
```

---

[Previous](./[16]-Built-in-Math-Types.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[18]-Classes-and-Scripts.md)