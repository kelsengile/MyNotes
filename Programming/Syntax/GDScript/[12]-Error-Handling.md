[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[13]-Arrays.md)

*Core Syntax*

# Lesson 12 - Error Handling & Defensive Coding

Every programmer writes bugs. What matters is how quickly you find them and how well your code protects itself. GDScript handles errors very differently from languages like Python or Java, so this lesson explains the tools you do have: assertions, error messages, checks, and error codes. It ends with a guide to reading the error messages you will meet most often.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 12.1 How GDScript Handles Errors (No try/except)

GDScript has **no exceptions**. There is no `try`, `catch`, `except`, or `throw`. You cannot "catch" a runtime error and carry on from the same spot.

What happens instead when something goes wrong at runtime:

1. Godot prints an **error message** to the Output and Debugger panels.
2. The **current function stops** at that line, and execution returns to the caller. (Often with `null` as the result.)
3. In the editor, the debugger may **pause** the game at the error so you can inspect it. Click **Continue** (`F12`) to resume.
4. The rest of the game usually keeps running, which can hide the problem and cause other strange behavior.

Because you cannot catch errors, **prevention** is the strategy. You write code that:

- **Checks** values before using them (is it `null`? is the index valid?).
- **Validates** input from players and files.
- **Reports** problems clearly (`push_error`, `assert`).
- **Returns** error codes or success flags from functions that can fail.

This style is called **defensive coding**: assume that things can go wrong, and handle those cases deliberately.

```gdscript
extends Node


func _ready() -> void:
	var numbers := [1, 2, 3]
	# print(numbers[10])        # Runtime error: Index 10 is out of bounds. The function stops here.
	if 10 < numbers.size():
		print(numbers[10])
	else:
		print("Index 10 does not exist.")
```

---

## 12.2 `assert()`

`assert(condition, message)` checks that something you **believe is always true** really is. If the condition is `false`, the game stops (in the editor and in debug builds) and shows your message.

```gdscript
extends Node


func divide(a: float, b: float) -> float:
	assert(b != 0.0, "divide(): b must not be zero")
	return a / b


func _ready() -> void:
	print(divide(10.0, 2.0))    # 5.0
	# print(divide(1.0, 0.0))   # Assertion failed: divide(): b must not be zero
```

Key facts:

- `assert()` is for **programmer mistakes** (bugs), not for expected situations such as a player typing bad input.
- In **exported release builds**, assertions are **removed entirely**, so they cost nothing, but they also do not protect the shipped game. Never put code with side effects inside `assert()`:

```gdscript
extends Node

var items := ["sword"]


func _ready() -> void:
	# BAD: items.pop_back() would not run in a release build!
	# assert(items.pop_back() != null)

	# GOOD: do the work first, then assert on the result.
	var removed = items.pop_back()
	assert(removed != null, "Expected an item to remove")
```

- The message is optional but strongly recommended.
- Use `assert` to document assumptions: "this array is never empty here", "this node always exists".

---

## 12.3 `push_error()` and `push_warning()`

These functions send messages to the **debugger's Errors tab** (and the Output panel) **without stopping** your game.

```gdscript
extends Node


func load_level(level_number: int) -> void:
	if level_number < 1:
		push_error("load_level(): level_number must be at least 1, got %d" % level_number)
		return
	if level_number > 50:
		push_warning("load_level(): level %d is unusually high" % level_number)
	print("Loading level ", level_number)


func _ready() -> void:
	load_level(0)     # Error message, function returns early
	load_level(99)    # Warning message, then continues
	load_level(3)     # Works normally
```

| Function | Shows as | Stops the game? | Use when |
| --- | --- | --- | --- |
| `push_error(msg)` | Red error | No (but the debugger may break) | Something is definitely wrong |
| `push_warning(msg)` | Yellow warning | No | Something is suspicious but survivable |
| `print_debug(msg)` | Normal text with the file and line | No | Quick debugging output |
| `assert(cond, msg)` | Red error | Yes, in debug builds | A bug that should never happen |

A typical pattern is **report, then return a safe value**:

```gdscript
extends Node


func get_item_name(items: Array, index: int) -> String:
	if index < 0 or index >= items.size():
		push_error("get_item_name(): invalid index %d" % index)
		return ""
	return items[index]
```

---

## 12.4 Checking for `null` and `is_instance_valid()`

The most common runtime error in Godot is using a `null` value. Always ask: *"Could this be null?"*

### Check before using

```gdscript
extends Node


func _ready() -> void:
	var button = get_node_or_null("StartButton")
	if button != null:
		button.text = "Play"
	else:
		push_warning("StartButton was not found")
```

Prefer `get_node_or_null()` over `get_node()` when the node might not exist. `get_node()` prints an error and returns `null` if the path is wrong.

### Using `if` with an object

An object variable is **truthy** when it holds a valid object, and falsy when it is `null`:

```gdscript
extends Node

var target: Node = null


func _process(_delta: float) -> void:
	if target:
		print("Chasing ", target.name)
```

### Freed objects and `is_instance_valid()`

A node that was deleted with `queue_free()` or `free()` is **no longer usable**, but a variable that still points to it is **not** automatically set to `null`. Using it causes an error ("Cannot call method on a previously freed instance").

`is_instance_valid(object)` tells you whether the object still exists:

```gdscript
extends Node

var enemy: Node = null


func _ready() -> void:
	enemy = Node.new()
	add_child(enemy)
	enemy.queue_free()          # Scheduled for deletion at the end of the frame


func _process(_delta: float) -> void:
	if is_instance_valid(enemy):
		print("Enemy still exists")
	else:
		print("Enemy is gone")
		set_process(false)
```

> Use `is_instance_valid()` whenever a reference might outlive its object, for example a stored target, a node held by a long-running coroutine, or a reference kept after `queue_free()`.

---

## 12.5 Error Codes and the `Error` Enum

Many engine functions that can fail return an **error code** from the global `Error` enum. `OK` (value `0`) means success. Anything else is a specific failure.

Common values:

| Constant | Meaning |
| --- | --- |
| `OK` | Success |
| `FAILED` | Generic failure |
| `ERR_UNAVAILABLE` | Something is unavailable |
| `ERR_FILE_NOT_FOUND` | A file does not exist |
| `ERR_FILE_CANT_OPEN` | A file could not be opened |
| `ERR_CANT_CONNECT` | Connection failed |
| `ERR_INVALID_PARAMETER` | A bad argument was supplied |
| `ERR_ALREADY_EXISTS` | The thing already exists |
| `ERR_PARSE_ERROR` | Text could not be parsed |
| `ERR_TIMEOUT` | The operation timed out |

Check the result and report it with `error_string()`:

```gdscript
extends Node


func _ready() -> void:
	var config := ConfigFile.new()
	var err := config.load("user://settings.cfg")

	if err != OK:
		print("Could not load settings: ", error_string(err))
	else:
		print("Settings loaded")
```

### Returning error codes from your own functions

You can follow the same convention in your own code:

```gdscript
extends Node


func save_name(player_name: String) -> Error:
	if player_name.is_empty():
		return ERR_INVALID_PARAMETER
	var file := FileAccess.open("user://name.txt", FileAccess.WRITE)
	if file == null:
		return FileAccess.get_open_error()
	file.store_string(player_name)
	return OK


func _ready() -> void:
	var result := save_name("Ada")
	if result == OK:
		print("Saved!")
	else:
		push_error("Save failed: " + error_string(result))
```

Some functions return `null` or `-1` instead of a code (for example `Array.find()` returns `-1`). Check each function's documentation (`F1`) to see how it reports failure.

---

## 12.6 Validating Input and Return Values

Anything that comes from **outside** your code is untrusted: player input, files, network data, and even values from other scripts. Validate before use.

### Validating text from the player

```gdscript
extends Node


func parse_age(text: String) -> int:
	var cleaned := text.strip_edges()
	if cleaned.is_empty():
		push_warning("Age is empty")
		return -1
	if not cleaned.is_valid_int():
		push_warning("Age is not a number: '%s'" % cleaned)
		return -1
	var age := cleaned.to_int()
	if age < 0 or age > 150:
		push_warning("Age out of range: %d" % age)
		return -1
	return age


func _ready() -> void:
	print(parse_age("  25 "))    # 25
	print(parse_age("abc"))      # -1
	print(parse_age("-5"))       # -1
```

### Validating data from a file or the network

```gdscript
extends Node


func load_player_data(json_text: String) -> Dictionary:
	var parsed = JSON.parse_string(json_text)
	if parsed == null:
		push_error("Invalid JSON")
		return {}
	if not parsed is Dictionary:
		push_error("Expected a dictionary")
		return {}
	if not parsed.has("name") or not parsed.has("hp"):
		push_error("Missing required fields")
		return {}
	return parsed


func _ready() -> void:
	print(load_player_data('{"name": "Ada", "hp": 10}'))   # { "name": "Ada", "hp": 10 }
	print(load_player_data("not json"))                    # {}
```

### Validating return values

Do not assume a function succeeded. If it can return `null`, `-1`, an empty array, or an error code, handle that case:

```gdscript
extends Node


func _ready() -> void:
	var scene = load("res://does_not_exist.tscn")
	if scene == null:
		push_error("Scene failed to load")
		return
	add_child(scene.instantiate())
```

Useful defensive habits:

- Use **guard clauses** at the top of a function to reject bad input early and `return`.
- Prefer `dictionary.get(key, default)` over `dictionary[key]` when a key might be missing.
- Check **array size** before indexing.
- Use **static typing** so the editor finds many problems before you run the game ([Lesson 17](./[17]-Static-Typing.md)).
- Do not silently ignore errors. Report them with `push_error()` so you notice them.

---

## 12.7 Common Beginner Errors and How to Read Them

Every message follows the same pattern: **the kind of error, a description, and the file and line where it happened.** Always start with the line number.

### 1. Invalid access to a property on `Nil`

```
Invalid access to property or key 'text' on a base object of type 'Nil'.
```

**Meaning:** You used `something.text`, but `something` is `null`.
**Typical causes:** The node path in `get_node()` or `$` is wrong, the node was not created yet, or a variable was never assigned.
**Fix:** Check the path or the order of initialization, and use `if something != null` or `get_node_or_null()`.

### 2. Invalid call. Nonexistent function

```
Invalid call. Nonexistent function 'move' in base 'Node2D'.
```

**Meaning:** The method name does not exist on that object (typo, wrong class, or the script is not attached).
**Fix:** Check the spelling and the documentation (`F1`). Make sure the node actually has your script.

### 3. Identifier not declared in the current scope

```
Identifier "speed" not declared in the current scope.
```

**Meaning:** You used a variable that does not exist where you used it. Perhaps it was declared inside another function, or misspelled.
**Fix:** Declare it at the right level (see scope in [Lesson 10](./[10]-Functions-and-Scope.md)).

### 4. Index out of bounds

```
Index 5 is out of bounds (size = 3).
```

**Meaning:** You accessed array position 5, but the array only has 3 elements (valid indexes are 0 to 2).
**Fix:** Check `array.size()` first.

### 5. Invalid get index (dictionary)

```
Invalid access to property or key 'score' on a base object of type 'Dictionary'.
```

**Meaning:** The key `"score"` is not in the dictionary.
**Fix:** Use `dict.has("score")` or `dict.get("score", 0)`.

### 6. Parse errors: indentation

```
Parse Error: Unexpected "Indent" in class body.
Parse Error: Used tab character for indentation instead of space as used before in the file.
```

**Meaning:** Tabs and spaces are mixed, or a line is indented more or less than it should be.
**Fix:** Use only tabs (the default). In the editor you can convert with **Edit > Convert Indent to Tabs**.

### 7. Cannot call a method on a freed instance

```
Attempt to call function 'queue_free' on a previously freed instance.
```

**Meaning:** The object was already deleted.
**Fix:** Check `is_instance_valid(obj)` first, and avoid keeping references to freed nodes.

### 8. Type mismatch

```
Cannot assign a value of type String to variable "health" with specified type int.
```

**Meaning:** A statically typed variable received the wrong kind of value.
**Fix:** Convert the value (`int(text)`), or use the right type.

### 9. Node not found

```
Node not found: "Player/Sprite2D" (relative to "/root/Main").
```

**Meaning:** `get_node()` or `$` could not find the node at that path.
**Fix:** Compare the path with the Scene dock exactly. Names are **case-sensitive**.

### A strategy for any error

1. Read the **first** error, because later ones are often a result of it.
2. Click the file and line number to jump to the code.
3. Read the message and identify the **type** of problem.
4. Use `print()` or a **breakpoint** (see [Lesson 44](./[44]-Debugging-and-Profiling.md)) to inspect the values on that line.
5. Search the built-in docs with `F1` if a function does not behave as you expect.

---

[Previous](./[11]-String-Formatting.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[13]-Arrays.md)