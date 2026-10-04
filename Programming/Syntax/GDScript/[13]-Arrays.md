[Previous](./[12]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[14]-Dictionaries.md)

*Data Structures*

# Lesson 13 - Arrays

An **array** is an ordered list of values stored in a single variable. Inventories, high-score tables, waypoints, enemies in a wave, and tile maps are all naturally stored in arrays. Arrays are one of the most used tools in GDScript, so this lesson covers them in detail.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 13.1 Arrays: Ordered & Mutable

You create an array with square brackets and commas:

```gdscript
extends Node


func _ready() -> void:
	var empty := []
	var numbers := [10, 20, 30]
	var names := ["Ada", "Bob", "Cy"]
	var mixed := [1, "two", 3.0, true, Vector2(4, 5)]   # Arrays can hold different types

	print(numbers)           # [10, 20, 30]
	print(numbers.size())    # 3
	print(empty.is_empty())  # true
	print(mixed)
```

Two important properties:

- **Ordered:** Each element has a fixed position (an **index**), and the order is kept exactly as you put it. Indexing starts at **0**.
- **Mutable:** You can change an array after creating it by adding, removing, and replacing elements.

```gdscript
extends Node


func _ready() -> void:
	var items := ["sword", "shield"]
	items[1] = "bow"          # Replace the element at index 1
	items.append("potion")    # Add to the end
	print(items)              # ["sword", "bow", "potion"]
	print("bow" in items)     # true
```

Other basics:

```gdscript
extends Node


func _ready() -> void:
	var a := [1, 2, 3]
	var b := [4, 5]
	print(a + b)              # [1, 2, 3, 4, 5]  (joins into a NEW array)
	print(a == [1, 2, 3])     # true             (compares the contents)
	print(a.size())           # 3
	print(a.front())          # 1  (first element)
	print(a.back())           # 3  (last element)
	a.clear()
	print(a)                  # []
```

You can loop through an array with `for` (see [Lesson 9](./[9]-Loops.md)):

```gdscript
extends Node


func _ready() -> void:
	for item in ["a", "b", "c"]:
		print(item)
```

---

## 13.2 Indexing, Negative Indexes, and Slicing

### Indexing

```gdscript
extends Node


func _ready() -> void:
	var letters := ["a", "b", "c", "d", "e"]
	print(letters[0])     # a   (first)
	print(letters[2])     # c
	print(letters[4])     # e   (last)
	print(letters[-1])    # e   (last, counting from the end)
	print(letters[-2])    # d
```

Using an index that does not exist (such as `letters[5]` here, or `letters[-6]`) is a runtime **error**: "Index out of bounds". Check with `size()` or use `array.get(index)`, which also requires a valid index. A safe pattern:

```gdscript
extends Node


func _ready() -> void:
	var letters := ["a", "b", "c"]
	var index := 7
	if index >= 0 and index < letters.size():
		print(letters[index])
	else:
		print("Invalid index")
```

### Slicing

`slice(begin, end = INT_MAX, step = 1, deep = false)` returns a **new array** containing a part of the original. The `end` index is **not** included.

```gdscript
extends Node


func _ready() -> void:
	var letters := ["a", "b", "c", "d", "e", "f"]
	print(letters.slice(1, 4))       # ["b", "c", "d"]
	print(letters.slice(2))          # ["c", "d", "e", "f"]  (to the end)
	print(letters.slice(0, 6, 2))    # ["a", "c", "e"]       (every second element)
	print(letters.slice(-3))         # ["d", "e", "f"]       (last three)
	print(letters.slice(0, -1))      # ["a", "b", "c", "d", "e"]  (all but the last)
	print(letters.slice(4, 0, -1))   # ["e", "d", "c", "b"]  (backwards)
```

---

## 13.3 Common Array Methods (`append`, `insert`, `erase`, `pop_back`, `sort`, `find`, `map`, `filter`, `reduce`)

### Adding elements

```gdscript
extends Node


func _ready() -> void:
	var a := [2, 3]
	a.append(4)             # [2, 3, 4]          add to the end (also push_back)
	a.push_front(1)         # [1, 2, 3, 4]       add to the beginning
	a.insert(2, 99)         # [1, 2, 99, 3, 4]   insert at index 2
	a.append_array([5, 6])  # [1, 2, 99, 3, 4, 5, 6]
	print(a)
```

### Removing elements

```gdscript
extends Node


func _ready() -> void:
	var a := [10, 20, 30, 40, 50, 20]
	a.erase(20)             # Removes the FIRST value 20      -> [10, 30, 40, 50, 20]
	a.remove_at(0)          # Removes by index                -> [30, 40, 50, 20]
	var last = a.pop_back() # Removes and returns the last    -> 20, array is [30, 40, 50]
	var first = a.pop_front()  # Removes and returns the first -> 30, array is [40, 50]
	var middle = a.pop_at(1)   # Removes and returns index 1   -> 50, array is [40]
	print(last, " ", first, " ", middle, " ", a)
```

`pop_back()` and `push_back()` make an array behave like a **stack** (last in, first out). `push_back()` with `pop_front()` makes a **queue** (first in, first out).

### Searching

```gdscript
extends Node


func _ready() -> void:
	var a := [5, 8, 3, 8, 1]
	print(a.find(8))        # 1   (first index of 8, or -1 if not found)
	print(a.rfind(8))       # 3   (search from the end)
	print(a.has(3))         # true
	print(a.count(8))       # 2
	print(a.min())          # 1
	print(a.max())          # 8
```

### Sorting and ordering

```gdscript
extends Node


func _ready() -> void:
	var a := [5, 2, 9, 1]
	a.sort()                # [1, 2, 5, 9]  (changes the array in place)
	a.reverse()             # [9, 5, 2, 1]
	a.shuffle()             # Random order
	print(a.size())

	var names := ["Cy", "Ada", "Bob"]
	names.sort()
	print(names)            # ["Ada", "Bob", "Cy"]
```

`sort()` changes the array and returns nothing, so write `a.sort()` rather than `a = a.sort()`. To sort with your own rule, use `sort_custom()` with a function that returns `true` when the **first** argument should come **before** the second:

```gdscript
extends Node


func _ready() -> void:
	var players := [
		{"name": "Ada", "score": 50},
		{"name": "Bob", "score": 90},
		{"name": "Cy", "score": 70},
	]
	players.sort_custom(func(a, b): return a["score"] > b["score"])   # Highest score first
	for p in players:
		print(p["name"], ": ", p["score"])
```

Output:

```
Bob: 90
Cy: 70
Ada: 50
```

The `func(a, b): ...` syntax is a **lambda**, an unnamed function (see [Lesson 39](./[39]-Lambdas-and-Callables.md)).

### Functional methods: `map`, `filter`, `reduce`, `any`, `all`

These take a function (usually a lambda) and apply it to the elements.

```gdscript
extends Node


func _ready() -> void:
	var numbers := [1, 2, 3, 4, 5, 6]

	var doubled := numbers.map(func(n): return n * 2)
	print(doubled)          # [2, 4, 6, 8, 10, 12]

	var evens := numbers.filter(func(n): return n % 2 == 0)
	print(evens)            # [2, 4, 6]

	var total = numbers.reduce(func(accumulator, n): return accumulator + n, 0)
	print(total)            # 21

	print(numbers.any(func(n): return n > 5))    # true  (at least one matches)
	print(numbers.all(func(n): return n > 0))    # true  (every one matches)
```

- `map` returns a **new** array with each element transformed.
- `filter` returns a **new** array of the elements for which the function returns `true`.
- `reduce` combines all elements into a single value. The second argument is the starting value.
- The original array is not changed by any of these.

---

## 13.4 Typed Arrays (`Array[int]`)

A **typed array** can only hold values of one type. This catches mistakes and makes code faster and clearer.

```gdscript
extends Node


func _ready() -> void:
	var scores: Array[int] = [10, 20, 30]
	var names: Array[String] = ["Ada", "Bob"]
	var positions: Array[Vector2] = [Vector2(0, 0), Vector2(5, 5)]
	var enemies: Array[Node2D] = []

	scores.append(40)         # OK
	# scores.append("fifty")  # ERROR: a String cannot go in an Array[int]
	print(scores)
	print(names, positions, enemies)
```

Additional details:

- You can use any built-in type or class: `Array[float]`, `Array[Node]`, `Array[Resource]`, or your own `class_name` types such as `Array[Enemy]`.
- When you loop over a typed array, the loop variable has the right type, so the editor knows what it is:

```gdscript
extends Node


func _ready() -> void:
	var typed: Array[int] = [1, 2, 3]
	for id in typed:
		print(id + 1)       # The editor knows id is an int
```

- Typed arrays work as function parameters and return values:

```gdscript
extends Node


func average(values: Array[float]) -> float:
	var total := 0.0
	for v in values:
		total += v
	return total / values.size()


func _ready() -> void:
	var samples: Array[float] = [1.0, 2.0, 6.0]
	print(average(samples))    # 3.0
```

An untyped `Array` cannot be passed where an `Array[float]` is expected, even if it holds only floats. Declare it with the right type from the start.

---

## 13.5 Multidimensional Arrays and Grids

GDScript has no special 2D array type. You make one by putting **arrays inside an array**: an array of rows, where each row is an array of cells.

```gdscript
extends Node


func _ready() -> void:
	var grid := [
		[1, 2, 3],
		[4, 5, 6],
		[7, 8, 9],
	]

	print(grid[0])       # [1, 2, 3]   (the first row)
	print(grid[1][2])    # 6           (row 1, column 2)

	grid[2][0] = 70
	print(grid[2])       # [70, 8, 9]

	for row in grid:
		print(row)
```

### Creating a grid of a chosen size

```gdscript
extends Node


func make_grid(width: int, height: int, fill_value = 0) -> Array:
	var grid := []
	for y in range(height):
		var row := []
		for x in range(width):
			row.append(fill_value)
		grid.append(row)
	return grid


func _ready() -> void:
	var map := make_grid(4, 3, ".")
	map[1][2] = "#"
	for row in map:
		print("".join(row))
```

Output:

```
....
..#.
....
```

> **Common mistake:** Do not build a grid with `var row := [0, 0, 0]` and then append **the same** `row` to the grid three times. All three entries would point to **one** array, so changing one cell would change every row. Create a **new** row inside the loop (as `make_grid` does above), or use `duplicate()`.

### Typed 2D arrays

```gdscript
extends Node


func _ready() -> void:
	var board: Array[Array] = []
	for y in range(3):
		var row: Array[int] = []
		row.resize(3)
		row.fill(0)
		board.append(row)
	print(board)    # [[0, 0, 0], [0, 0, 0], [0, 0, 0]]
```

`resize(n)` changes the array length (new slots are filled with the default value for the type), and `fill(value)` sets every element to the same value.

### A flat array as a grid

For large grids, a single **flat** array with the index `y * width + x` is faster and uses less memory:

```gdscript
extends Node

const WIDTH := 4
const HEIGHT := 3

var cells: Array[int] = []


func _ready() -> void:
	cells.resize(WIDTH * HEIGHT)
	cells.fill(0)
	cells[2 * WIDTH + 1] = 9        # x = 1, y = 2
	print(cells)
```

---

## 13.6 Packed Arrays (`PackedInt32Array`, `PackedVector2Array`, etc.)

**Packed arrays** are specialized arrays that store one kind of value in a compact block of memory. They use less memory and are faster than normal arrays, which matters when you handle large amounts of data, such as mesh vertices, image pixels, or audio samples.

| Type | Stores |
| --- | --- |
| `PackedByteArray` | Bytes (0 to 255) |
| `PackedInt32Array` / `PackedInt64Array` | 32-bit / 64-bit integers |
| `PackedFloat32Array` / `PackedFloat64Array` | 32-bit / 64-bit floats |
| `PackedStringArray` | Strings |
| `PackedVector2Array` / `PackedVector3Array` / `PackedVector4Array` | Vectors |
| `PackedColorArray` | Colors |

```gdscript
extends Node


func _ready() -> void:
	var ints := PackedInt32Array([1, 2, 3])
	ints.append(4)
	ints.push_back(5)
	print(ints)              # [1, 2, 3, 4, 5]
	print(ints[0])           # 1

	var points := PackedVector2Array([Vector2(0, 0), Vector2(10, 5)])
	points.append(Vector2(20, 10))
	print(points.size())     # 3

	var words := PackedStringArray(["a", "b"])
	words.append("c")
	print(", ".join(words))  # a, b, c

	var bytes := "Hi".to_utf8_buffer()      # PackedByteArray
	print(bytes)                            # [72, 105]
```

When to use them:

- Many engine functions use them: `String.split()` returns a `PackedStringArray`, `Line2D.points` is a `PackedVector2Array`, and `FileAccess.get_buffer()` returns a `PackedByteArray`.
- Convert a normal array to a packed one with the constructor and back with `Array(packed)`.
- They do not hold mixed types.
- Packed arrays have most of the common methods (`append`, `insert`, `size`, `sort`, `find`, `slice`, and so on), but not the functional ones such as `map` and `filter`.
- Like normal arrays, packed arrays are **passed by reference** in Godot 4, so use `duplicate()` when you need an independent copy.

For everyday small lists, a normal `Array` (preferably typed) is fine.

---

## 13.7 Copying Arrays: Shallow vs Deep (`duplicate()`)

Arrays are **reference types**. Assigning an array to another variable does **not** copy it. Both variables point to the **same** array:

```gdscript
extends Node


func _ready() -> void:
	var a := [1, 2, 3]
	var b := a              # b refers to the SAME array
	b.append(4)
	print(a)                # [1, 2, 3, 4]  (a changed too!)
```

### Shallow copy

`duplicate()` creates a **new** array with the same elements:

```gdscript
extends Node


func _ready() -> void:
	var a := [1, 2, 3]
	var b := a.duplicate()
	b.append(4)
	print(a)                # [1, 2, 3]
	print(b)                # [1, 2, 3, 4]
```

But a shallow copy copies only the **top level**. If the array contains other arrays, dictionaries, or objects, the copy still points to the **same** inner items:

```gdscript
extends Node


func _ready() -> void:
	var original := [[1, 2], [3, 4]]
	var shallow := original.duplicate()
	shallow[0].append(99)
	print(original)         # [[1, 2, 99], [3, 4]]   (the inner array was shared!)
```

### Deep copy

`duplicate(true)` copies **nested** arrays and dictionaries as well:

```gdscript
extends Node


func _ready() -> void:
	var original := [[1, 2], [3, 4]]
	var deep := original.duplicate(true)
	deep[0].append(99)
	print(original)         # [[1, 2], [3, 4]]       (unchanged)
	print(deep)             # [[1, 2, 99], [3, 4]]
```

Notes:

- `duplicate(true)` copies nested **arrays and dictionaries**. Objects such as nodes are never duplicated that way. The copy keeps the same reference to them. A node must be duplicated with its own `duplicate()` method.
- Use `slice(0)` as another way to make a quick shallow copy.
- Parameters passed to functions are references too: a function that modifies an array parameter changes the caller's array (see [Lesson 10](./[10]-Functions-and-Scope.md)). Pass `array.duplicate()` if the function should not touch the original.

---

[Previous](./[12]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[14]-Dictionaries.md)