[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[7]-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 6 - Numbers, Strings & Booleans

Numbers, text, and true/false values are the simplest and most used data types. Almost every script you write will use them. This lesson looks at each in detail, including the important special values `null` and `Variant`.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 6.1 Integers and Floats

GDScript has two number types:

- **`int`**: a whole number with no decimal point, such as `-3`, `0`, `42`. It is a 64-bit signed integer.
- **`float`**: a number with a decimal part, such as `3.14`, `-0.5`, `2.0`. It is a 64-bit floating-point number.

```gdscript
extends Node


func _ready() -> void:
	var lives: int = 3
	var speed: float = 250.5
	var ratio := 0.75
	var big := 9_000_000_000     # Underscores are allowed for readability

	print(typeof(lives) == TYPE_INT)     # true
	print(typeof(speed) == TYPE_FLOAT)   # true
	print(big)
```

### Integer division

When **both** sides of a `/` are integers, GDScript performs **integer division** and throws away the remainder:

```gdscript
extends Node


func _ready() -> void:
	print(7 / 2)       # 3    (int / int = int)
	print(7 / 2.0)     # 3.5  (one side is a float, so the result is a float)
	print(7.0 / 2)     # 3.5
	print(float(7) / 2)  # 3.5
```

This is one of the most common beginner surprises. If you want a decimal result, make at least one side a float. GDScript shows an `INTEGER_DIVISION` warning to help you notice it.

### Float precision

Floats cannot store every decimal exactly. Tiny rounding errors are normal:

```gdscript
extends Node


func _ready() -> void:
	print(0.1 + 0.2)                        # 0.3 (printing rounds the value)
	print(0.1 + 0.2 == 0.3)                 # false (the stored values differ slightly)
	print(is_equal_approx(0.1 + 0.2, 0.3))  # true
```

**Never compare floats with `==`** when they come from calculations. Use `is_equal_approx(a, b)` for general comparisons and `is_zero_approx(x)` to compare with zero.

### Special float values

```gdscript
extends Node


func _ready() -> void:
	print(INF)           # inf  (infinity)
	print(-INF)          # -inf
	print(NAN)           # nan  ("not a number")
	print(is_nan(NAN))   # true
	print(PI)            # 3.14159...
	print(TAU)           # 6.28318... (2 * PI)
```

---

## 6.2 Arithmetic with Numbers and Built-in Math Functions

### Basic arithmetic

```gdscript
extends Node


func _ready() -> void:
	print(5 + 3)    # 8   addition
	print(5 - 3)    # 2   subtraction
	print(5 * 3)    # 15  multiplication
	print(10 / 4)   # 2   integer division
	print(10.0 / 4) # 2.5 float division
	print(10 % 3)   # 1   modulo (remainder)
	print(2 ** 3)   # 8   power
```

More operators are covered in [Lesson 7](./[7]-Operators-and-Expressions.md).

### Built-in math functions

Godot provides many global math functions. You can call them anywhere without importing anything.

```gdscript
extends Node


func _ready() -> void:
	print(abs(-5))             # 5
	print(sign(-12))           # -1
	print(min(3, 8))           # 3
	print(max(3, 8))           # 8
	print(clamp(15, 0, 10))    # 10   (limits a value to a range)
	print(sqrt(16.0))          # 4.0
	print(pow(2, 10))          # 1024.0
	print(floor(3.7))          # 3.0
	print(ceil(3.2))           # 4.0
	print(round(3.5))          # 4.0
	print(snappedf(3.14159, 0.01))  # 3.14
```

### Integer-returning versions

Many functions have a variant ending in `i` that returns an `int`, and a variant ending in `f` that works with floats. These are handy with static typing.

```gdscript
extends Node


func _ready() -> void:
	var a: int = floori(3.7)    # 3
	var b: int = ceili(3.2)     # 4
	var c: int = roundi(3.5)    # 4
	var d: int = absi(-4)       # 4
	var e: int = clampi(15, 0, 10)   # 10
	var f: float = clampf(1.5, 0.0, 1.0)  # 1.0
	print(a, b, c, d, e, f)
```

### Shortcuts for angles

```gdscript
extends Node


func _ready() -> void:
	print(deg_to_rad(180.0))   # 3.14159...
	print(rad_to_deg(PI))      # 180.0
```

Godot uses **radians** for rotation in code (even though the Inspector shows degrees for convenience). More math functions, such as `lerp()` and trigonometry, appear in [Lesson 38](./[38]-Randomness-and-Math.md).

---

## 6.3 Strings: Creation and Basics

A **String** is a sequence of characters. Create one with double quotes or single quotes:

```gdscript
extends Node


func _ready() -> void:
	var a := "Hello"
	var b := 'World'
	var c := "She said \"hi\""     # Escaped quotes inside a string
	var d := 'It\'s fine'
	var e := "It's fine"           # Or just use the other kind of quote
	print(a, " ", b, " ", c, " ", d, " ", e)
```

### Joining (concatenation)

Use `+` to join strings. Only strings can be added to strings, so convert other types with `str()`:

```gdscript
extends Node


func _ready() -> void:
	var name := "Ada"
	var greeting := "Hello, " + name + "!"
	print(greeting)                       # Hello, Ada!

	var level := 5
	print("Level: " + str(level))         # Level: 5
	# print("Level: " + level)            # ERROR: cannot add a String and an int
```

Use `*` to repeat a string:

```gdscript
extends Node


func _ready() -> void:
	print("-".repeat(10))   # ----------
	print("ab".repeat(3))   # ababab
```

### Length, indexing, and comparison

```gdscript
extends Node


func _ready() -> void:
	var word := "Godot"
	print(word.length())      # 5
	print(word[0])            # G  (first character; counting starts at 0)
	print(word[-1])           # t  (last character)
	print(word == "Godot")    # true  (case-sensitive)
	print(word == "godot")    # false
	print(word.to_lower() == "godot")   # true
	print("apple" < "banana") # true (alphabetical comparison)
```

### Strings are values

Strings cannot be modified character by character. Methods **return a new string** and leave the original untouched:

```gdscript
extends Node


func _ready() -> void:
	var s := "hello"
	s.to_upper()          # Returns "HELLO" but we ignored it. s is still "hello".
	print(s)              # hello
	s = s.to_upper()      # Store the result to keep it
	print(s)              # HELLO
```

String formatting, slicing, and methods get their own lesson: [Lesson 11](./[11]-String-Formatting.md).

---

## 6.4 `String`, `StringName`, and `NodePath`

GDScript has three related text types. They look similar, but each has a distinct purpose.

### `String`

The normal text type. Use it for anything shown to the player or manipulated as text.

### `StringName`

A `StringName` is a **unique, interned string** optimized for fast comparison. The engine uses it internally for names of things like input actions, signals, animations, and node names. You create one with `&"..."`:

```gdscript
extends Node


func _ready() -> void:
	var action := &"jump"
	print(action)                        # jump
	print(action == &"jump")             # true
	print(action == "jump")              # true (they compare equal to a String)
	print(typeof(action) == TYPE_STRING_NAME)   # true
```

You rarely *need* to write `&"..."` yourself because plain strings are converted automatically where a `StringName` is expected. It is used mostly for slightly better performance in things like repeated `Input.is_action_pressed(&"jump")` calls. You can convert in both directions with `StringName("text")` and `str(name)`.

### `NodePath`

A `NodePath` describes the **location of a node** in the scene tree. You create one with `^"..."`:

```gdscript
extends Node


func _ready() -> void:
	var path := ^"Player/Sprite2D"
	print(path)                       # Player/Sprite2D
	print(path.get_name_count())      # 2
	print(path.get_name(0))           # Player
```

Common path forms:

| Path | Meaning |
| --- | --- |
| `^"Child"` | A direct child called `Child` |
| `^"Child/Grandchild"` | A deeper descendant |
| `^".."` | The parent |
| `^"../Sibling"` | A sibling node |
| `^"/root/Main"` | An absolute path from the root of the tree |
| `^"Sprite2D:position"` | The `position` **property** of the `Sprite2D` node |

`NodePath` is also the type of `@export` variables that point to other nodes, and the parameter type of `get_node()`. Using nodes is covered in [Lesson 24](./[24]-Nodes-and-Scenes.md).

---

## 6.5 Booleans and Truthiness

A **`bool`** has only two possible values: `true` and `false`. Booleans power every decision in your code.

```gdscript
extends Node


func _ready() -> void:
	var is_alive: bool = true
	var has_key := false

	print(is_alive)       # true
	print(not has_key)    # true
	print(5 > 3)          # true (comparisons produce booleans)
	print(5 == 3)         # false
```

Name boolean variables like questions: `is_alive`, `has_key`, `can_jump`, `is_visible`.

### Truthiness

When a non-boolean value is used where a boolean is expected (for example in an `if`), GDScript treats it as either "truthy" or "falsy":

| Value | Truthiness |
| --- | --- |
| `false`, `null` | falsy |
| `0`, `0.0` | falsy |
| `""` (empty string) | falsy |
| `[]` (empty array), `{}` (empty dictionary) | falsy |
| `Vector2(0, 0)` | falsy |
| Non-zero numbers, non-empty strings, non-empty arrays and dictionaries | truthy |
| An object (such as a valid node) | truthy |

```gdscript
extends Node


func _ready() -> void:
	print(bool(0))            # false
	print(bool(5))            # true
	print(bool(""))           # false
	print(bool("hi"))         # true
	print(bool([]))           # false
	print(bool([0]))          # true  (the array is not empty)
	print(bool(null))         # false
```

Truthiness lets you write short code such as `if inventory:` instead of `if inventory.size() > 0:`. Beginners should favor the explicit version until the shortcut feels natural. Conditions are covered in [Lesson 8](./[8]-Conditionals.md).

---

## 6.6 `null` and the `Variant` Concept

### `null`

`null` means **"no value"**. A variable that has been declared without a value, a node that was not found, or a failed lookup are typical sources:

```gdscript
extends Node


func _ready() -> void:
	var target = null
	print(target)              # <null>
	print(target == null)      # true

	var missing = get_node_or_null("DoesNotExist")
	print(missing)             # <null>
```

Trying to use `null` as if it were a real object is the most common runtime error in Godot:

```gdscript
extends Node


func _ready() -> void:
	var missing = get_node_or_null("DoesNotExist")
	# missing.visible = false   # ERROR: Invalid access to property on a base object of type 'Nil'
	if missing != null:
		missing.visible = false  # Safe: only runs when the node exists
```

Always check a value that **might** be `null` before you use it. See [Lesson 12](./[12]-Error-Handling.md).

Note that the built-in value types (`int`, `float`, `bool`, `String`, `Vector2`, and so on) can **never** be `null` when they are statically typed. Only objects (such as nodes and resources) and untyped `Variant` variables can hold `null`.

### `Variant`

**`Variant`** is the universal type that can hold **any** value: numbers, strings, vectors, arrays, objects, even `null`. Every untyped variable is a `Variant`, and many engine functions use it for parameters or return values.

```gdscript
extends Node


func _ready() -> void:
	var anything                # Implicitly a Variant
	anything = 5
	anything = "now text"
	anything = [1, 2, 3]

	var explicit: Variant = 3.14   # An explicit Variant
	print(anything, explicit)
```

Why it matters:

- A `Variant` is **flexible** but gives you **no type safety** and no specific auto-completion.
- When you read a value out of an `Array` or `Dictionary` (which hold Variants), you often want to give it a more specific type:

```gdscript
extends Node


func _ready() -> void:
	var data := {"coins": 12}
	var coins: int = data["coins"]    # Explicitly typed from a Variant
	print(coins + 1)                  # 13
```

- Typed code that keeps `Variant` to a minimum is both safer and faster. See [Lesson 17](./[17]-Static-Typing.md).

---

[Previous](./[5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[7]-Operators-and-Expressions.md)