[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[9]-Loops.md)

*Core Syntax*

# Lesson 8 - Conditionals: if, elif, else, match

Programs need to make decisions: *if* the player has a key, open the door; *otherwise*, show a message. **Conditional statements** let your code choose between different paths. This lesson covers `if`, `elif`, `else`, the ternary expression, truthiness, and Godot's powerful `match` statement.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 8.1 The `if` Statement

An `if` statement runs a block of code **only when** its condition is `true`.

```gdscript
extends Node


func _ready() -> void:
	var health := 20

	if health < 30:
		print("Warning: low health!")

	print("This line always runs.")
```

The structure:

```
if condition:
	indented code that runs when the condition is true
```

Key points:

- The line ends with a **colon** `:`.
- The block of code below is **indented** (one tab). Everything at that indentation level belongs to the `if`.
- The condition does **not** need parentheses, although you may add them for clarity.
- When the condition is `false`, the whole block is skipped.

```gdscript
extends Node


func _ready() -> void:
	var has_key := true
	var door_locked := true

	if has_key and door_locked:
		print("You unlock the door.")
		door_locked = false
```

---

## 8.2 `elif` and `else`

Use `else` to run code when the condition is **false**, and `elif` (short for "else if") to test additional conditions in order.

```gdscript
extends Node


func _ready() -> void:
	var score := 72

	if score >= 90:
		print("Grade: A")
	elif score >= 80:
		print("Grade: B")
	elif score >= 70:
		print("Grade: C")
	else:
		print("Grade: F")
```

The output is `Grade: C`.

How it works:

1. Conditions are checked **from top to bottom**.
2. The **first** one that is `true` runs, and the rest are skipped.
3. `else` (optional) runs only when **none** of the conditions were true.

Order matters. If you checked `score >= 70` first, a score of 95 would print "Grade: C" because that condition matches first.

```gdscript
extends Node


func _ready() -> void:
	var is_raining := false

	if is_raining:
		print("Take an umbrella.")
	else:
		print("Enjoy the sun.")
```

You can have any number of `elif` branches but only one `if` at the start and at most one `else` at the end.

---

## 8.3 Nested Conditionals

You can place an `if` inside another `if`. This is called **nesting**.

```gdscript
extends Node


func _ready() -> void:
	var is_alive := true
	var health := 15

	if is_alive:
		if health < 20:
			print("You are alive but weak.")
		else:
			print("You are alive and strong.")
	else:
		print("Game over.")
```

Each level of nesting adds another level of indentation. Deep nesting is hard to read, so consider these two simplifications:

**Combine conditions with `and`:**

```gdscript
extends Node


func _ready() -> void:
	var is_alive := true
	var health := 15

	if is_alive and health < 20:
		print("You are alive but weak.")
```

**Use early returns ("guard clauses") inside functions:**

```gdscript
extends Node


func attack(target: Node, stamina: int) -> void:
	if target == null:
		return
	if stamina <= 0:
		print("Too tired to attack.")
		return

	print("Attacking ", target.name)
```

Handling the "bad" cases first and returning keeps the main logic at a single indentation level.

---

## 8.4 Conditional (Ternary) Expressions

A one-line `if`/`else` that produces a value is called a **ternary expression**. The format is `value_if_true if condition else value_if_false`.

```gdscript
extends Node


func _ready() -> void:
	var age := 20
	var category := "adult" if age >= 18 else "minor"
	print(category)   # adult

	var speed := 400.0 if Input.is_action_pressed("sprint") else 200.0
	print(speed)
```

It is equivalent to:

```gdscript
extends Node


func _ready() -> void:
	var age := 20
	var category: String
	if age >= 18:
		category = "adult"
	else:
		category = "minor"
	print(category)
```

Use a ternary for **short, simple** choices. For anything longer or nested, a normal `if` statement is clearer.

> **Note:** GDScript does not have a `?:` operator like C-style languages. The `x if cond else y` form is the only ternary syntax.

---

## 8.5 Truthy and Falsy Values in Conditions

A condition does not have to be a `bool`. GDScript converts any value into `true` or `false` automatically (see [Lesson 6](./[6]-Numbers-Strings-and-Booleans.md)).

```gdscript
extends Node


func _ready() -> void:
	var items := []
	var player_name := "Ada"
	var target = null

	if items:
		print("Has items")
	else:
		print("Inventory is empty")      # This runs: an empty array is falsy

	if player_name:
		print("Hello, ", player_name)    # This runs: a non-empty string is truthy

	if target:
		print("Has a target")
	else:
		print("No target")               # This runs: null is falsy
```

Falsy values: `false`, `null`, `0`, `0.0`, `""`, `[]`, `{}`, and zero vectors. Everything else is truthy.

Be careful with numbers. `0` is a perfectly valid value for many variables (such as a score of zero), so `if score:` is `false` when the score is `0`. When zero is meaningful, compare explicitly:

```gdscript
extends Node


func _ready() -> void:
	var score := 0

	if score:
		print("Truthy")
	if score != null:
		print("There is a score, even though it is zero.")
```

For a freed node (an object that has been deleted but a variable still points to), use `is_instance_valid(node)` rather than truthiness (see [Lesson 12](./[12]-Error-Handling.md)).

---

## 8.6 The `match` Statement (Pattern Matching)

`match` compares one value against several **patterns** and runs the block for the first pattern that fits. It is like a `switch` statement in other languages but more powerful.

```gdscript
extends Node


func _ready() -> void:
	var day := 3

	match day:
		1:
			print("Monday")
		2:
			print("Tuesday")
		3:
			print("Wednesday")
		_:
			print("Some other day")
```

The output is `Wednesday`.

Key points:

- The value to test comes right after `match` and ends with a colon.
- Each pattern is followed by a colon and an **indented** block.
- The underscore `_` is the **wildcard** pattern. It matches anything and acts like `else`. Put it last.
- Only the **first** matching pattern runs. There is **no fall-through** and no `break` is needed. (Each branch ends on its own.)
- You can list **several patterns** for one branch, separated by commas:

```gdscript
extends Node


func _ready() -> void:
	var day := 6

	match day:
		1, 2, 3, 4, 5:
			print("Weekday")
		6, 7:
			print("Weekend")
		_:
			print("Invalid day")
```

`match` works with strings too:

```gdscript
extends Node


func _on_command(command: String) -> void:
	match command:
		"start":
			print("Starting...")
		"stop", "quit":
			print("Stopping...")
		_:
			print("Unknown command: ", command)
```

Use `continue` inside a `match` branch to jump to the **next** matching pattern, if you want a fall-through behavior on purpose:

```gdscript
extends Node


func _ready() -> void:
	match 5:
		5:
			print("Matched 5")
			continue
		_:
			print("Also ran the wildcard")
```

---

## 8.7 Match Patterns: Constants, Variables, Arrays, Dictionaries, Wildcards, Binding

`match` supports several kinds of patterns.

### Constant patterns

Numbers, strings, `true`, `false`, `null`, and constants (including enum values) match when equal:

```gdscript
extends Node

enum State { IDLE, RUN, JUMP }


func describe(state: State) -> String:
	match state:
		State.IDLE:
			return "Standing still"
		State.RUN:
			return "Running"
		State.JUMP:
			return "In the air"
	return "Unknown"
```

### Variable (binding) patterns

A **new name** in a pattern matches **anything** and stores the value in that new variable, which is available only in that branch:

```gdscript
extends Node


func _ready() -> void:
	match 42:
		0:
			print("Zero")
		var value:
			print("Got the number ", value)   # Got the number 42
```

Use the `var` keyword before the name to bind. It acts like a wildcard that also remembers the value.

### Wildcard pattern

`_` matches anything and does not store it (shown earlier).

### Array patterns

An array pattern matches arrays of the **same length** whose elements match each sub-pattern. The `..` pattern means "any number of remaining elements" and must come last.

```gdscript
extends Node


func _ready() -> void:
	var data := [1, 2]

	match data:
		[]:
			print("Empty array")
		[1]:
			print("Only the number 1")
		[1, var second]:
			print("Starts with 1, then ", second)   # Starts with 1, then 2
		[1, 2, ..]:
			print("Starts with 1, 2 and maybe more")
```

### Dictionary patterns

A dictionary pattern matches dictionaries that contain the listed keys, whose values match the sub-patterns. Extra keys are allowed only with `..`:

```gdscript
extends Node


func _ready() -> void:
	var event := {"type": "damage", "amount": 15}

	match event:
		{"type": "heal", "amount": var amount}:
			print("Healed ", amount)
		{"type": "damage", "amount": var amount}:
			print("Took ", amount, " damage")   # Took 15 damage
		{"type": "damage", ..}:
			print("Damage of unknown size")
		_:
			print("Unknown event")
```

Patterns are checked **in order**, so put the most specific patterns first.

### Mixing patterns

You can combine them. Here a function reacts to different shapes of data:

```gdscript
extends Node


func handle(message) -> void:
	match message:
		null:
			print("Nothing")
		true:
			print("Yes")
		"quit":
			print("Quit requested")
		[var x, var y]:
			print("Point at ", x, ", ", y)
		{"name": var n}:
			print("Named thing: ", n)
		var other:
			print("Something else: ", other)
```

> **Note:** `match` patterns compare **values** and structure, not ranges. To test a range such as "between 10 and 20", use `if`/`elif` instead. A common trick is `match true:` followed by `if` guards, but a plain `if`/`elif` chain is clearer.

### Choosing between `if` and `match`

| Use `if` / `elif` | Use `match` |
| --- | --- |
| Conditions with ranges (`x > 10`) | Comparing one value to several exact options |
| Combining unrelated conditions | Enum states, command strings, input types |
| Few branches | Many branches |
| | Inspecting the shape of arrays or dictionaries |

---

[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[9]-Loops.md)