[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[10]-Functions-and-Scope.md)

*Core Syntax*

# Lesson 9 - Loops: for & while, break, continue, pass

A **loop** repeats a block of code. Instead of writing `print` ten times, you write it once and tell GDScript to repeat it. GDScript has two loop types: `for` (repeat for each item) and `while` (repeat as long as a condition is true). This lesson also covers the keywords that control loops, and an important warning about how loops relate to Godot's game loop.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 9.1 The `for` Loop

A `for` loop goes through a **sequence of values**, one at a time, running its block for each value.

```gdscript
extends Node


func _ready() -> void:
	for number in [10, 20, 30]:
		print(number)
```

Output:

```
10
20
30
```

The structure:

```
for variable in sequence:
	code that runs once per item
```

- `variable` is a new name created by the loop. It holds the current item and exists only inside the loop.
- `sequence` can be an array, a dictionary, a string, a `range()`, or any other collection.
- You can add a type to the loop variable:

```gdscript
extends Node


func _ready() -> void:
	for number: int in [1, 2, 3]:
		print(number * 2)
```

Typed loop variables (`for x: int in ...`) are available from Godot 4.2. The type is checked, which helps catch errors.

---

## 9.2 The `range()` Function

`range()` generates a sequence of **integers**. It is the standard way to repeat something a certain number of times.

| Call | Produces |
| --- | --- |
| `range(5)` | `0, 1, 2, 3, 4` |
| `range(2, 6)` | `2, 3, 4, 5` |
| `range(0, 10, 2)` | `0, 2, 4, 6, 8` |
| `range(10, 0, -3)` | `10, 7, 4, 1` |

The **end value is never included**, and counting starts at `0` by default.

```gdscript
extends Node


func _ready() -> void:
	for i in range(3):
		print("Hello number ", i)    # i is 0, 1, 2

	for i in range(1, 4):
		print(i)                     # 1, 2, 3

	for i in range(0, 10, 3):
		print(i)                     # 0, 3, 6, 9

	for i in range(5, 0, -1):
		print(i)                     # 5, 4, 3, 2, 1
```

You can also write `for i in 5:` as a shortcut for `for i in range(5):`:

```gdscript
extends Node


func _ready() -> void:
	for i in 3:
		print(i)    # 0, 1, 2
```

`range()` is useful for **repeating** something, and for visiting array positions by index:

```gdscript
extends Node


func _ready() -> void:
	var names := ["Ada", "Bob", "Cy"]
	for i in range(names.size()):
		print(i, ": ", names[i])
```

Output:

```
0: Ada
1: Bob
2: Cy
```

---

## 9.3 Looping Over Arrays, Dictionaries, and Strings

### Arrays

The loop variable receives each **element**:

```gdscript
extends Node


func _ready() -> void:
	var fruits := ["apple", "banana", "cherry"]
	for fruit in fruits:
		print("I like ", fruit)
```

### Dictionaries

Looping over a dictionary gives you its **keys**. Use the key to look up the value:

```gdscript
extends Node


func _ready() -> void:
	var stats := {"hp": 100, "mp": 40, "speed": 5}

	for key in stats:
		print(key, " = ", stats[key])

	for value in stats.values():
		print(value)

	for key in stats.keys():
		print(key)
```

Dictionaries keep the order in which keys were inserted.

### Strings

Looping over a string gives you each **character** (as a one-character string):

```gdscript
extends Node


func _ready() -> void:
	for letter in "Godot":
		print(letter)     # G, o, d, o, t
```

### Other things you can loop over

- Packed arrays such as `PackedStringArray` and `PackedInt32Array`.
- Node children: `for child in get_children():`
- Integers: `for i in 5:`
- Any object that implements the iterator methods (`_iter_init`, `_iter_next`, `_iter_get`), which is an advanced technique.

```gdscript
extends Node


func _ready() -> void:
	for child in get_children():
		print("Child: ", child.name)
```

### Do not modify an array while looping over it

Removing or inserting elements while looping can skip items or cause bugs:

```gdscript
extends Node


func _ready() -> void:
	var numbers := [1, 2, 3, 4, 5, 6]

	# Safe approach: build a new array with the items you want to keep.
	var odd_numbers := []
	for n in numbers:
		if n % 2 == 1:
			odd_numbers.append(n)
	print(odd_numbers)   # [1, 3, 5]

	# Another safe approach: loop over a copy.
	for n in numbers.duplicate():
		if n % 2 == 0:
			numbers.erase(n)
	print(numbers)       # [1, 3, 5]
```

---

## 9.4 The `while` Loop

A `while` loop repeats **as long as** its condition stays `true`. The condition is checked **before** each repetition.

```gdscript
extends Node


func _ready() -> void:
	var count := 0
	while count < 3:
		print("Count is ", count)
		count += 1
	print("Done")
```

Output:

```
Count is 0
Count is 1
Count is 2
Done
```

Use `while` when you do **not** know in advance how many repetitions you need:

```gdscript
extends Node


func _ready() -> void:
	var value := 100
	var halvings := 0
	while value > 1:
		value /= 2
		halvings += 1
	print("It took ", halvings, " halvings")   # It took 6 halvings
```

### Beware of infinite loops

If the condition never becomes `false`, the loop never ends and your game **freezes**:

```gdscript
extends Node


func _ready() -> void:
	var x := 0
	# while x < 10:
	#	print(x)       # x never changes: this would run forever and freeze the editor!
```

Always make sure something inside the loop changes the condition. If a loop freezes the game, stop it with the Stop button, or close the window.

GDScript has **no** `do...while` loop. To run the body at least once, use `while true:` with a `break`:

```gdscript
extends Node


func _ready() -> void:
	var tries := 0
	while true:
		tries += 1
		print("Try number ", tries)
		if tries >= 3:
			break
```

---

## 9.5 `break`, `continue`, and `pass`

### `break` stops the loop completely

```gdscript
extends Node


func _ready() -> void:
	for i in range(10):
		if i == 4:
			break
		print(i)    # 0, 1, 2, 3
```

### `continue` skips to the next repetition

```gdscript
extends Node


func _ready() -> void:
	for i in range(6):
		if i % 2 == 0:
			continue     # Skip even numbers
		print(i)         # 1, 3, 5
```

### `pass` does nothing

`pass` is a placeholder statement. It is used where code is **required** syntactically, but you have nothing to write yet:

```gdscript
extends Node


func take_damage(amount: int) -> void:
	pass    # TODO: implement later


func _ready() -> void:
	for i in range(3):
		pass     # Empty loop body (not useful, just allowed)
```

### Common pattern: searching

```gdscript
extends Node


func _ready() -> void:
	var enemies := ["slime", "bat", "goblin", "bat"]
	var found_index := -1

	for i in range(enemies.size()):
		if enemies[i] == "goblin":
			found_index = i
			break      # No need to keep searching

	print("Goblin at index ", found_index)   # 2
```

> **Note:** In a `match` block, `continue` has a different meaning: it moves to the next matching pattern ([Lesson 8](./[8]-Conditionals.md)). Inside a loop, `break` and `continue` always affect the **innermost loop**.

---

## 9.6 Nested Loops

A loop inside another loop is a **nested loop**. The inner loop runs completely for every single repetition of the outer loop.

```gdscript
extends Node


func _ready() -> void:
	for row in range(3):
		for col in range(3):
			print("(", row, ", ", col, ")")
```

This prints 9 lines, from `(0, 0)` to `(2, 2)`. A common use is going through a grid:

```gdscript
extends Node


func _ready() -> void:
	var grid_width := 4
	var grid_height := 3

	for y in range(grid_height):
		var line := ""
		for x in range(grid_width):
			line += "#"
		print(line)
```

Output:

```
####
####
####
```

### `break` in nested loops

`break` exits only the **innermost** loop. To leave both loops, use a flag variable or move the loops into a function and `return`:

```gdscript
extends Node


func find_pair(target: int) -> Vector2i:
	for a in range(1, 10):
		for b in range(1, 10):
			if a * b == target:
				return Vector2i(a, b)    # Leaves both loops at once
	return Vector2i(-1, -1)


func _ready() -> void:
	print(find_pair(42))   # (6, 7)
```

Be careful with performance: two nested loops of 1,000 each run 1,000,000 times.

---

## 9.7 Why Loops Don't Replace `_process()`

Beginners sometimes try to animate or wait using a loop. This does **not** work in a game engine, and understanding why is important.

Godot runs a **game loop**: many times per second, it processes input, runs your `_process()` and `_physics_process()` functions, and then **draws a frame**. Between frames, the screen cannot update.

A `while` or `for` loop inside one function runs **all at once**, within a single frame. The screen is not redrawn until your function finishes:

```gdscript
extends Sprite2D


# WRONG: the sprite will appear to jump instantly, not move smoothly
func _ready() -> void:
	for i in range(100):
		position.x += 5
```

All 100 steps happen before the next frame is drawn, so the player sees only the final result. Worse, a long or infinite loop **freezes the whole game**, because nothing else (drawing, input) can run until the loop is done.

The correct approach is to do a **small step each frame** using `_process()`, scaled by `delta` (the time since the last frame):

```gdscript
extends Sprite2D

var speed := 100.0


# RIGHT: a little movement every frame
func _process(delta: float) -> void:
	position.x += speed * delta
```

If you need something to happen **over time**, use one of these tools instead of a loop:

| You want to... | Use |
| --- | --- |
| Do something every frame | `_process(delta)` or `_physics_process(delta)` |
| Wait a few seconds, then continue | `await get_tree().create_timer(2.0).timeout` ([Lesson 29](./[29]-Coroutines-and-Await.md)) |
| Smoothly change a value over time | A `Tween` ([Lesson 31](./[31]-Timers-and-Tweens.md)) |
| Do something repeatedly on a schedule | A `Timer` node ([Lesson 31](./[31]-Timers-and-Tweens.md)) |

Loops are still **perfect** for work that should be done instantly: processing a list of items, building a grid, searching for something, or spawning a fixed number of enemies at once. Use them for *data*, and use `_process()` for *time*.

---

[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[10]-Functions-and-Scope.md)