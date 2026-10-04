[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[11]-String-Formatting.md)

*Core Syntax*

# Lesson 10 - Functions & Scope

A **function** is a named, reusable block of code. Instead of copying the same lines in many places, you write them once, give them a name, and **call** that name whenever you need them. Functions make code shorter, easier to read, and easier to fix. This lesson covers how to define functions, how to pass data in and out, and where variables are visible (scope).

---

## 10.1 Defining and Calling Functions (`func`)

You define a function with the `func` keyword, a name, parentheses, a colon, and an indented body:

```gdscript
extends Node


func say_hello() -> void:
	print("Hello!")
	print("Welcome to the game.")


func _ready() -> void:
	say_hello()     # Calling the function runs its body
	say_hello()     # You can call it as many times as you like
```

Output:

```
Hello!
Welcome to the game.
Hello!
Welcome to the game.
```

Points to remember:

- Function names use `snake_case` and should describe an **action**: `take_damage`, `spawn_enemy`, `calculate_score`.
- Defining a function does **not** run it. Only calling it (with parentheses) runs it.
- The order of function definitions in the file does not matter. You can call a function that is written further down.
- Special functions such as `_ready()` and `_process()` are called by **Godot itself** at specific moments. You define them, and the engine calls them (see [Lesson 25](./[25]-Lifecycle-and-Callbacks.md)).

---

## 10.2 Parameters, Arguments & Return Values

### Parameters and arguments

A **parameter** is a variable in the function's definition. An **argument** is the actual value you pass in when calling it.

```gdscript
extends Node


func greet(player_name, level) -> void:
	print("Welcome, ", player_name, "! You are level ", level)


func _ready() -> void:
	greet("Ada", 5)       # "Ada" and 5 are arguments
	greet("Bob", 12)
```

Arguments are matched to parameters **by position**: the first argument goes to the first parameter, and so on. Passing the wrong number of arguments is an error.

### Return values

A function can send a value **back** to the caller with `return`:

```gdscript
extends Node


func add(a, b):
	return a + b


func _ready() -> void:
	var total = add(3, 4)
	print(total)           # 7
	print(add(10, 20))     # 30
```

Facts about `return`:

- `return` **ends** the function immediately. Code after it in the same path does not run.
- A function without a `return` (or with a bare `return`) gives back `null`.
- You can have several `return` statements, for example in different branches:

```gdscript
extends Node


func absolute(x):
	if x < 0:
		return -x
	return x


func _ready() -> void:
	print(absolute(-8))   # 8
	print(absolute(8))    # 8
```

Functions that only **do** something (print, move, play a sound) are usually written with `-> void`. Functions that **calculate** something return a value.

---

## 10.3 Default Arguments

A parameter can have a **default value**, used when the caller leaves it out:

```gdscript
extends Node


func heal(amount: int = 10) -> void:
	print("Healed for ", amount)


func create_label(text: String, size: int = 16, color: Color = Color.WHITE) -> void:
	print(text, " size=", size, " color=", color)


func _ready() -> void:
	heal()          # Healed for 10
	heal(50)        # Healed for 50
	create_label("Hi")                        # uses both defaults
	create_label("Hi", 24)                    # overrides size only
	create_label("Hi", 24, Color.RED)         # overrides both
```

Rules:

- Parameters **with** defaults must come **after** parameters without defaults.
- GDScript has **no named arguments**. You cannot skip `size` and set `color` by name. You must pass the arguments in order.
- A default is calculated each time the function is called, not once when it is defined, so using `[]` or `{}` as a default is safe in GDScript.

---

## 10.4 Typed Parameters and Return Types (`-> int`)

You can declare the type of each parameter and of the return value. This is **static typing** applied to functions:

```gdscript
extends Node


func add(a: int, b: int) -> int:
	return a + b


func get_greeting(name: String) -> String:
	return "Hello, " + name


func is_dead(health: int) -> bool:
	return health <= 0


func log_message(text: String) -> void:
	print(text)


func _ready() -> void:
	print(add(2, 3))              # 5
	print(get_greeting("Ada"))    # Hello, Ada
	print(is_dead(0))             # true
	log_message("Typed!")
```

The syntax is:

```
func name(parameter: Type, ...) -> ReturnType:
```

Benefits:

- Passing the wrong kind of value (for example `add("a", 1)`) is flagged **immediately** in the editor.
- The editor offers better auto-completion.
- A typed function declares its intent clearly: readers see exactly what goes in and comes out.
- Typed code is generally faster.

### Special return types

- `-> void` means the function returns nothing. Using `return value` in it is an error.
- You can return engine types such as `Node`, `Vector2`, `Array`, or your own class names.
- `-> Variant` means "anything", the same as having no return type but stated clearly.

```gdscript
extends Node


func make_vector(x: float, y: float) -> Vector2:
	return Vector2(x, y)


func find_child_named(parent: Node, wanted: String) -> Node:
	return parent.get_node_or_null(wanted)
```

Typing is covered fully in [Lesson 17](./[17]-Static-Typing.md).

---

## 10.5 Returning Multiple Values (Arrays/Dictionaries)

A function can return only **one** value. To give back several pieces of data, put them in an array or a dictionary.

### Using an array

```gdscript
extends Node


func min_and_max(numbers: Array) -> Array:
	return [numbers.min(), numbers.max()]


func _ready() -> void:
	var result := min_and_max([4, 9, 1, 7])
	print(result[0])    # 1  (minimum)
	print(result[1])    # 9  (maximum)
```

### Using a dictionary (clearer, because each value has a name)

```gdscript
extends Node


func analyze(numbers: Array) -> Dictionary:
	return {
		"min": numbers.min(),
		"max": numbers.max(),
		"count": numbers.size(),
	}


func _ready() -> void:
	var info := analyze([4, 9, 1, 7])
	print(info["min"])      # 1
	print(info["max"])      # 9
	print(info.count)       # 4  (dictionary keys can also be read with dot syntax)
```

### Using a `Vector2` for two numbers

When your two values are numbers, a `Vector2` is a handy lightweight option:

```gdscript
extends Node


func split_time(total_seconds: int) -> Vector2i:
	return Vector2i(total_seconds / 60, total_seconds % 60)


func _ready() -> void:
	var t := split_time(125)
	print(t.x, " min ", t.y, " sec")   # 2 min 5 sec
```

Arrays and dictionaries are explored in [Lesson 13](./[13]-Arrays.md) and [Lesson 14](./[14]-Dictionaries.md). Larger bundles of data are best handled with custom classes or resources ([Lesson 23](./[23]-Custom-Resources.md)).

---

## 10.6 Variable-Argument Workarounds

Some languages allow a function to accept any number of arguments (`*args`). **GDScript does not support this** for your own functions. There are three common workarounds.

### Workaround 1: Accept an array

The caller passes the items in one array:

```gdscript
extends Node


func sum_all(numbers: Array) -> int:
	var total := 0
	for n in numbers:
		total += n
	return total


func _ready() -> void:
	print(sum_all([1, 2, 3, 4]))    # 10
	print(sum_all([]))              # 0
```

### Workaround 2: Use default arguments

Give optional parameters defaults of `null` and check for them:

```gdscript
extends Node


func spawn(scene_name: String, x: float = 0.0, y: float = 0.0, parent = null) -> void:
	print("Spawning ", scene_name, " at ", Vector2(x, y))
	if parent != null:
		print("Under parent ", parent.name)


func _ready() -> void:
	spawn("Enemy")
	spawn("Enemy", 100.0, 50.0)
	spawn("Enemy", 100.0, 50.0, self)
```

### Workaround 3: Accept a dictionary of options

```gdscript
extends Node


func create_unit(options: Dictionary = {}) -> void:
	var unit_name: String = options.get("name", "Unnamed")
	var hp: int = options.get("hp", 10)
	var flying: bool = options.get("flying", false)
	print(unit_name, " hp=", hp, " flying=", flying)


func _ready() -> void:
	create_unit()
	create_unit({"name": "Bat", "flying": true})
```

This "options dictionary" works like named arguments. The trade-off is that typos in the keys are not caught by the editor.

Note that **calling** built-in variadic functions such as `print()` and `max()` works with many arguments because they are written in the engine. Only your own functions are affected by this limit. For callables, `callv(array)` lets you call a function using an array as its argument list (see [Lesson 39](./[39]-Lambdas-and-Callables.md)).

---

## 10.7 Local, Member, and Global Scope

**Scope** is the part of your code where a variable can be seen and used.

### Local scope

A variable declared **inside a function** (or inside an `if`/`for` block within it) exists only there:

```gdscript
extends Node


func calculate() -> void:
	var result := 42        # Local to calculate()
	print(result)


func _ready() -> void:
	calculate()
	# print(result)         # ERROR: "result" is not declared in this scope
```

A variable declared inside a block (such as a loop or `if`) lives only until that block ends:

```gdscript
extends Node


func _ready() -> void:
	for i in range(3):
		var squared := i * i     # A new variable on every repetition
		print(squared)
	# print(i)                   # ERROR: i only exists inside the loop
	# print(squared)             # ERROR: squared only exists inside the loop
```

### Member scope

A variable declared at the **top level of a script** is a **member variable** of the class. Every function in that script can read and change it, and it lasts as long as the object exists:

```gdscript
extends Node

var score := 0           # Member variable


func add_points(points: int) -> void:
	score += points      # Accessible here


func _ready() -> void:
	add_points(10)
	add_points(5)
	print(score)         # 15
```

### Shadowing

If a local variable has the **same name** as a member variable, the local one **hides** (shadows) the member inside that function. GDScript warns you about this, since it is often a mistake:

```gdscript
extends Node

var speed := 100


func _ready() -> void:
	var speed := 5       # Warning: shadows the member variable "speed"
	print(speed)         # 5   (the local one)
	print(self.speed)    # 100 (use self. to reach the member)
```

Choose different names to avoid confusion.

### Global scope

GDScript has **no** global variables in the traditional sense. Each script's variables belong to that script's class. However, the following are available everywhere:

- **Built-in functions and constants** such as `print()`, `PI`, `INF`.
- **Global classes** registered with `class_name` (for example, `Vector2`, or your own `class_name Player`).
- **Singletons** such as `Input`, `OS`, `Engine`, and `ProjectSettings`.
- **Autoloads**: scripts or scenes registered in Project Settings that exist for the whole game and can be accessed by name from any script. This is how you share data such as the player's score between scenes (see [Lesson 30](./[30]-Groups-and-Autoloads.md)).
- **Constants** and **enums** declared with `class_name` are reachable through the class name.

### Function parameters are local

Parameters behave like local variables. For simple types (numbers, strings, booleans, vectors), changing a parameter inside the function does **not** affect the caller's variable. Arrays, dictionaries, and objects are passed by **reference**, so changes to their contents **are** visible to the caller:

```gdscript
extends Node


func change_number(n: int) -> void:
	n = 99                  # Only changes the local copy


func change_array(arr: Array) -> void:
	arr.append(99)          # Changes the caller's array too


func _ready() -> void:
	var x := 1
	change_number(x)
	print(x)                # 1  (unchanged)

	var list := [1, 2]
	change_array(list)
	print(list)             # [1, 2, 99]  (changed)
```

---

## 10.8 Recursion

A **recursive** function is one that **calls itself**. It solves a problem by breaking it into smaller copies of the same problem. Every recursive function needs:

1. A **base case**: a condition where it stops and returns without calling itself.
2. A **recursive case**: a call to itself with a **smaller or simpler** input.

### Example: factorial

`5! = 5 * 4 * 3 * 2 * 1 = 120`

```gdscript
extends Node


func factorial(n: int) -> int:
	if n <= 1:                    # Base case
		return 1
	return n * factorial(n - 1)   # Recursive case


func _ready() -> void:
	print(factorial(5))    # 120
```

How `factorial(3)` unfolds:

```
factorial(3) = 3 * factorial(2)
             = 3 * (2 * factorial(1))
             = 3 * (2 * 1)
             = 6
```

### Example: counting down

```gdscript
extends Node


func countdown(n: int) -> void:
	if n < 0:
		return
	print(n)
	countdown(n - 1)


func _ready() -> void:
	countdown(3)    # 3, 2, 1, 0
```

### Example: walking through the scene tree

Recursion is a natural fit for **tree structures** such as nodes and their children:

```gdscript
extends Node


func print_tree(node: Node, depth: int = 0) -> void:
	print("  ".repeat(depth) + node.name)
	for child in node.get_children():
		print_tree(child, depth + 1)


func _ready() -> void:
	print_tree(self)
```

### Cautions

- **Missing base case:** The function never stops and eventually causes a **stack overflow** error that crashes the game.
- **Depth limit:** Godot limits how deep calls can go. Very deep recursion (tens of thousands of levels) fails.
- **Performance:** Each call has overhead. When a simple loop does the job, a loop is usually faster and safer. For example, `factorial` can be written with a `for` loop.

---

[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[11]-String-Formatting.md)