[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[8]-Conditionals.md)

*Core Syntax*

# Lesson 7 - Operators & Expressions

An **operator** is a symbol or keyword that performs an action on values, such as adding two numbers or comparing them. An **expression** is any piece of code that produces a value, for example `2 + 3` or `health > 0`. This lesson covers all of GDScript's operators and the order in which they are evaluated.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 7.1 Arithmetic Operators

| Operator | Name | Example | Result |
| --- | --- | --- | --- |
| `+` | Addition | `5 + 2` | `7` |
| `-` | Subtraction | `5 - 2` | `3` |
| `*` | Multiplication | `5 * 2` | `10` |
| `/` | Division | `5.0 / 2` | `2.5` |
| `%` | Modulo (remainder) | `5 % 2` | `1` |
| `**` | Power | `2 ** 3` | `8` |
| `-` (unary) | Negation | `-x` | flips the sign |

```gdscript
extends Node


func _ready() -> void:
	print(10 + 4)      # 14
	print(10 - 4)      # 6
	print(10 * 4)      # 40
	print(10 / 4)      # 2    (both are int, so integer division)
	print(10 / 4.0)    # 2.5
	print(10 % 4)      # 2
	print(2 ** 5)      # 32
	print(-(3 + 2))    # -5
```

Details to remember:

- `%` works on **integers**. For floats, use `fmod(a, b)`: `fmod(5.5, 2.0)` gives `1.5`. `fposmod()` and `posmod()` always return a non-negative result, which is useful for wrapping indices.
- Dividing an integer by `0` causes an error. Dividing a float by `0.0` gives `inf` (or `nan`).
- Operators also work on other types: `Vector2(1, 2) + Vector2(3, 4)` gives `(4, 6)`, and `"ab" + "cd"` joins strings. Some operators have special meaning on strings: `"Score: %d" % 10` is a format (see [Lesson 11](./[11]-String-Formatting.md)).

```gdscript
extends Node


func _ready() -> void:
	print(posmod(-1, 5))                  # 4
	print(-1 % 5)                         # -1
	print(Vector2(1, 2) + Vector2(3, 4))  # (4, 6)
	print(Vector2(1, 2) * 3)              # (3, 6)
```

---

## 7.2 Comparison Operators

Comparison operators compare two values and produce a `bool`.

| Operator | Meaning | Example | Result |
| --- | --- | --- | --- |
| `==` | Equal to | `3 == 3` | `true` |
| `!=` | Not equal to | `3 != 4` | `true` |
| `<` | Less than | `3 < 4` | `true` |
| `>` | Greater than | `3 > 4` | `false` |
| `<=` | Less than or equal | `3 <= 3` | `true` |
| `>=` | Greater than or equal | `3 >= 4` | `false` |

```gdscript
extends Node


func _ready() -> void:
	var health := 50
	print(health == 50)    # true
	print(health != 50)    # false
	print(health > 75)     # false
	print(health <= 100)   # true

	print("a" == "a")      # true
	print("a" == "A")      # false (case matters)
	print([1, 2] == [1, 2])  # true (arrays compare element by element)
	print(1 == 1.0)        # true (int and float compare by value)
```

A very common mistake is writing `=` (assignment) when you mean `==` (comparison). GDScript will report an error if you try `if x = 5:`.

Remember not to compare computed floats with `==`. Use `is_equal_approx()` instead (see [Lesson 6](./[6]-Numbers-Strings-and-Booleans.md)).

---

## 7.3 Logical Operators (`and`, `or`, `not`, `&&`, `||`, `!`)

Logical operators combine booleans. Each has a **word form** and a **symbol form**, and they are interchangeable:

| Word | Symbol | Meaning |
| --- | --- | --- |
| `and` | `&&` | True when **both** sides are true |
| `or` | `\|\|` | True when **at least one** side is true |
| `not` | `!` | Reverses true and false |

The word forms are preferred in GDScript style because they read like English.

```gdscript
extends Node


func _ready() -> void:
	var is_alive := true
	var has_key := false
	var has_lockpick := true

	print(is_alive and has_key)                  # false
	print(is_alive or has_key)                   # true
	print(not has_key)                           # true
	print(has_key or has_lockpick)               # true
	print(is_alive and (has_key or has_lockpick))  # true
	print(is_alive && !has_key)                  # true (symbol forms)
```

### Truth table

| `a` | `b` | `a and b` | `a or b` |
| --- | --- | --- | --- |
| true | true | true | true |
| true | false | false | true |
| false | true | false | true |
| false | false | false | false |

### Short-circuit evaluation

GDScript stops evaluating as soon as the result is known:

- With `and`, if the left side is `false`, the right side is **never evaluated**.
- With `or`, if the left side is `true`, the right side is **never evaluated**.

This lets you write safe checks:

```gdscript
extends Node


func _ready() -> void:
	var target = null
	# Safe: if target is null, the second half is skipped, so no error occurs.
	if target != null and target.visible:
		print("Visible target")
	else:
		print("No visible target")
```

---

## 7.4 Assignment Operators

The basic assignment operator `=` stores a value in a variable. The **compound** assignment operators combine an operation with assignment:

| Operator | Equivalent to |
| --- | --- |
| `x += 5` | `x = x + 5` |
| `x -= 5` | `x = x - 5` |
| `x *= 5` | `x = x * 5` |
| `x /= 5` | `x = x / 5` |
| `x %= 5` | `x = x % 5` |
| `x **= 2` | `x = x ** 2` |
| `x &= 5` | `x = x & 5` (bitwise and) |
| `x \|= 5` | `x = x \| 5` (bitwise or) |
| `x ^= 5` | `x = x ^ 5` (bitwise xor) |
| `x <<= 1` | `x = x << 1` |
| `x >>= 1` | `x = x >> 1` |

```gdscript
extends Node


func _ready() -> void:
	var score := 10
	score += 5      # 15
	score -= 3      # 12
	score *= 2      # 24
	score /= 4      # 6
	score %= 4      # 2
	print(score)

	var text := "Hello"
	text += ", World"   # Works on strings too
	print(text)         # Hello, World
```

Important notes:

- GDScript has **no** `++` or `--` operators. Write `x += 1` or `x -= 1`.
- Assignment is a **statement**, not an expression, so you cannot write `if (x = 5):` or `a = b = 0`.
- Multiple variables can be given the same value only by separate statements.

---

## 7.5 Bitwise Operators

Bitwise operators work on the individual **bits** of integers. They are used for flags and masks, most famously in Godot's **collision layers**.

| Operator | Name | Example | Result |
| --- | --- | --- | --- |
| `&` | AND | `0b1100 & 0b1010` | `0b1000` (8) |
| `\|` | OR | `0b1100 \| 0b1010` | `0b1110` (14) |
| `^` | XOR | `0b1100 ^ 0b1010` | `0b0110` (6) |
| `~` | NOT (invert) | `~0b0001` | `-2` |
| `<<` | Shift left | `1 << 3` | `8` |
| `>>` | Shift right | `16 >> 2` | `4` |

```gdscript
extends Node


func _ready() -> void:
	print(0b1100 & 0b1010)   # 8
	print(0b1100 | 0b1010)   # 14
	print(0b1100 ^ 0b1010)   # 6
	print(1 << 3)            # 8
	print(16 >> 2)           # 4
```

### Practical use: flags

```gdscript
extends Node

const FLAG_FIRE = 1 << 0     # 1
const FLAG_ICE = 1 << 1      # 2
const FLAG_POISON = 1 << 2   # 4


func _ready() -> void:
	var effects := 0
	effects |= FLAG_FIRE           # Turn FIRE on
	effects |= FLAG_POISON         # Turn POISON on

	print((effects & FLAG_FIRE) != 0)   # true  (FIRE is on)
	print((effects & FLAG_ICE) != 0)    # false (ICE is off)

	effects &= ~FLAG_FIRE          # Turn FIRE off
	print((effects & FLAG_FIRE) != 0)   # false
```

Collision layers and masks use the same idea: layer 1 is bit `1`, layer 2 is bit `2`, layer 3 is bit `4`, and so on (see [Lesson 32](./[32]-2D-Physics-and-Movement.md)).

---

## 7.6 Membership Operator (`in`)

`in` tests whether a value is **contained** in something. It returns a `bool`.

```gdscript
extends Node


func _ready() -> void:
	var fruits := ["apple", "banana", "cherry"]
	print("apple" in fruits)          # true
	print("grape" in fruits)          # false
	print("grape" not in fruits)      # true

	var stats := {"hp": 10, "mp": 5}
	print("hp" in stats)              # true  (checks the KEYS)
	print(10 in stats)                # false (values are not checked)

	print("ell" in "Hello")           # true  (substring check)
	print(3 in range(5))              # true
```

What `in` checks depends on the container:

| Container | `x in container` checks |
| --- | --- |
| Array | Whether `x` is one of the elements |
| Dictionary | Whether `x` is one of the **keys** |
| String | Whether `x` is a substring |
| Object | Whether `x` is the name of a property |

`not in` is the opposite. The word `in` is also used in `for` loops (`for item in items:`), where it has a different meaning, "take each element in turn" (see [Lesson 9](./[9]-Loops.md)).

---

## 7.7 Type Operators (`is`, `as`)

These two operators work with types. They were introduced in [Lesson 5](./[5]-Variables-and-Data-Types.md) and are summarized here as part of the operator family.

### `is` (type check)

```gdscript
extends Node


func _ready() -> void:
	var value = 5
	print(value is int)         # true
	print(value is String)      # false
	print(value is not String)  # true

	var node: Node = Node2D.new()
	print(node is Node2D)       # true
	print(node is Sprite2D)     # false
	node.free()
```

### `as` (type cast)

```gdscript
extends Node


func _ready() -> void:
	var node: Node = Node2D.new()
	var node_2d := node as Node2D       # A Node2D
	var sprite := node as Sprite2D      # null: it is not a Sprite2D
	print(node_2d, " ", sprite)
	node.free()
```

A typical pattern is to check with `is` and then use the object:

```gdscript
extends Node


func _on_hit(body: Node) -> void:
	if body is CharacterBody2D:
		print("Hit a character at ", body.global_position)
```

---

## 7.8 The Ternary Expression

The **ternary expression** chooses between two values based on a condition, in a single line. GDScript writes it as:

```
value_if_true if condition else value_if_false
```

```gdscript
extends Node


func _ready() -> void:
	var health := 30
	var status := "Healthy" if health > 50 else "Hurt"
	print(status)                         # Hurt

	var number := -7
	var absolute := number if number >= 0 else -number
	print(absolute)                       # 7
```

Notes:

- Only the chosen branch is evaluated.
- It is an **expression**, so you can use it inside function arguments, assignments, and returns.
- It can be nested, but nested ternaries quickly become hard to read. Use a regular `if` statement in that case.

```gdscript
extends Node


func _ready() -> void:
	var coins := 1
	print("You have %d coin%s" % [coins, "" if coins == 1 else "s"])   # You have 1 coin
```

The ternary expression is also covered in [Lesson 8](./[8]-Conditionals.md).

---

## 7.9 Operator Precedence

When an expression has several operators, **precedence** decides which is evaluated first, just like in math class (multiplication before addition). Here is the order, from **highest** (evaluated first) to **lowest**:

| Priority | Operator(s) | Notes |
| --- | --- | --- |
| 1 (highest) | `( )` | Parentheses |
| 2 | `x[index]`, `x.attribute`, `x()` | Subscript, attribute access, function call |
| 3 | `await x` | Await |
| 4 | `x is Type`, `x is not Type` | Type check |
| 5 | `**` | Power |
| 6 | `~` | Bitwise NOT |
| 7 | unary `+`, `-` | Sign |
| 8 | `*`, `/`, `%` | Multiply, divide, modulo |
| 9 | `+`, `-` | Add, subtract |
| 10 | `<<`, `>>` | Bit shifts |
| 11 | `&` | Bitwise AND |
| 12 | `^` | Bitwise XOR |
| 13 | `\|` | Bitwise OR |
| 14 | `==`, `!=`, `<`, `>`, `<=`, `>=` | Comparisons |
| 15 | `x in y`, `x not in y` | Membership |
| 16 | `not`, `!` | Logical NOT |
| 17 | `and`, `&&` | Logical AND |
| 18 | `or`, `\|\|` | Logical OR |
| 19 | `x if cond else y` | Ternary |
| 20 (lowest) | `=`, `+=`, `-=`, ... | Assignment |

Examples:

```gdscript
extends Node


func _ready() -> void:
	print(2 + 3 * 4)        # 14   (multiplication first)
	print((2 + 3) * 4)      # 20   (parentheses first)
	print(2 ** 3 ** 2)      # 512  (power is evaluated right to left: 2 ** 9)
	print(-2 ** 2)          # -4   (power binds tighter than unary minus)
	print(10 - 4 - 3)       # 3    (left to right)

	var a := true
	var b := false
	var c := true
	print(a or b and c)     # true (and is evaluated before or)
	print((a or b) and c)   # true
	print(not a == b)       # true  (read as not (a == b), because == binds tighter than not)
```

Look closely at the last line. Comparison operators have **higher** precedence than `not`, so the expression is read as `not (a == b)`. Here `a == b` is `false`, so `not false` is `true`. Writing `not (a == b)` yourself, or better `a != b`, makes the intent clearer.

> **Best practice:** When an expression mixes several kinds of operators (including `as`, which is easy to misread), add parentheses to make your intent obvious, even where they are not required. Clear code beats clever code.

---

[Previous](./[6]-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[8]-Conditionals.md)