[Previous](./[13]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[15]-Enums-and-Constants.md)

*Data Structures*

# Lesson 14 - Dictionaries

An array finds items by **position** (`items[2]`). A **dictionary** finds items by a **name** you choose (`stats["health"]`). Dictionaries store **key-value pairs**, which makes them perfect for things like a character's stats, game settings, or data loaded from a JSON file.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 14.1 What is a Dictionary?

A dictionary is a collection of **pairs**. Each pair has:

- A **key**: the unique name used to look the value up.
- A **value**: the data stored under that key.

```
Key        Value
"name"  -> "Ada"
"level" -> 7
"alive" -> true
```

Features:

- **Keys are unique.** Assigning to an existing key replaces its value.
- **Lookup is fast**, no matter how many entries there are.
- **Keys and values can be any type.** Strings and integers are the most common keys, but vectors, enums, and even objects work.
- **Insertion order is preserved** when you loop through a dictionary.
- Dictionaries are **mutable** and are **reference types**, just like arrays (see [Lesson 13](./[13]-Arrays.md)).

Use an **array** for an ordered list of similar things. Use a **dictionary** when each item has a meaningful name or ID.

---

## 14.2 Creating and Accessing Dictionaries (including Lua-style syntax)

### Creating

```gdscript
extends Node


func _ready() -> void:
	var empty := {}
	var player := {
		"name": "Ada",
		"level": 7,
		"alive": true,
	}
	print(player)
	print(empty.is_empty())    # true
```

A trailing comma after the last pair is allowed and makes it easy to add entries later.

### Lua-style syntax

When keys are simple strings (letters, digits, and underscores, not starting with a digit), you can write them without quotes by using `=` instead of `:`:

```gdscript
extends Node


func _ready() -> void:
	var player := {
		name = "Ada",
		level = 7,
		alive = true,
	}
	print(player)           # { "name": "Ada", "level": 7, "alive": true }
```

This is **exactly** the same as quoting the keys. It is only a shorter way to write string keys. It does not work for keys that are numbers or other types.

### Accessing values

```gdscript
extends Node


func _ready() -> void:
	var player := {"name": "Ada", "level": 7}

	print(player["name"])       # Ada     (square brackets)
	print(player.name)          # Ada     (dot syntax, for string keys that are valid names)
	print(player.get("level"))  # 7
	print(player.get("gold", 0))  # 0      (a default when the key is missing)
```

### Missing keys

Reading a key that does not exist with `[]` or `.` is a runtime **error**. Use `get()` with a default, or check with `has()` first:

```gdscript
extends Node


func _ready() -> void:
	var player := {"name": "Ada"}

	# print(player["gold"])      # ERROR: Invalid access to key 'gold'
	print(player.get("gold"))     # <null>  (no error)
	print(player.get("gold", 0))  # 0

	if player.has("gold"):
		print("Has gold")
```

### Different kinds of keys

```gdscript
extends Node


func _ready() -> void:
	var table := {
		1: "one",
		2: "two",
		Vector2i(0, 1): "north",
		true: "yes",
	}
	print(table[1])                # one
	print(table[Vector2i(0, 1)])   # north
```

Vector keys are commonly used for grid positions: `grid[Vector2i(3, 4)] = "tree"`.

---

## 14.3 Adding, Updating, Removing Items

### Adding and updating

Assigning to a key **creates** it if it does not exist and **replaces** the value if it does:

```gdscript
extends Node


func _ready() -> void:
	var inventory := {"apple": 3}

	inventory["banana"] = 5      # Add a new key
	inventory["apple"] = 10      # Update an existing key
	inventory["apple"] += 1      # Compound assignment works
	inventory.pear = 2           # Dot syntax also works for assigning

	print(inventory)             # { "apple": 11, "banana": 5, "pear": 2 }
```

### Safe incrementing

Using `+=` on a missing key is an error. Use `get()` with a default:

```gdscript
extends Node


func _ready() -> void:
	var counts := {}
	for word in ["a", "b", "a", "c", "a"]:
		counts[word] = counts.get(word, 0) + 1
	print(counts)    # { "a": 3, "b": 1, "c": 1 }
```

`get_or_add(key, default)` returns the value for a key, adding it with the default first if it is missing (Godot 4.3 and later).

### Removing

```gdscript
extends Node


func _ready() -> void:
	var inventory := {"apple": 3, "banana": 5, "pear": 2}

	inventory.erase("banana")        # Removes the pair. Returns true if it existed.
	print(inventory)                 # { "apple": 3, "pear": 2 }

	print(inventory.erase("nothing"))   # false (no error)

	inventory.clear()                # Removes everything
	print(inventory.size())          # 0
```

### Size and emptiness

```gdscript
extends Node


func _ready() -> void:
	var d := {"a": 1, "b": 2}
	print(d.size())        # 2
	print(d.is_empty())    # false
```

---

## 14.4 Iterating Over Dictionaries

A `for` loop over a dictionary gives you its **keys**, in insertion order:

```gdscript
extends Node


func _ready() -> void:
	var prices := {"sword": 100, "shield": 80, "potion": 15}

	for item in prices:
		print(item, " costs ", prices[item])
```

Output:

```
sword costs 100
shield costs 80
potion costs 15
```

You can also loop over `keys()` or `values()` explicitly, or over both together:

```gdscript
extends Node


func _ready() -> void:
	var prices := {"sword": 100, "shield": 80, "potion": 15}

	for key in prices.keys():
		print("Key: ", key)

	for value in prices.values():
		print("Value: ", value)

	var total := 0
	for value in prices.values():
		total += value
	print("Total: ", total)       # 195
```

> **Careful:** Do not add or remove keys while looping over the same dictionary. Loop over `dict.keys()` (which is a copy of the keys) when you need to erase entries.

```gdscript
extends Node


func _ready() -> void:
	var stock := {"apple": 0, "pear": 4, "plum": 0}
	for key in stock.keys():
		if stock[key] == 0:
			stock.erase(key)
	print(stock)    # { "pear": 4 }
```

---

## 14.5 Dictionary Methods (`keys`, `values`, `has`, `get`, `merge`)

### Reading

```gdscript
extends Node


func _ready() -> void:
	var d := {"a": 1, "b": 2, "c": 3}

	print(d.keys())                    # ["a", "b", "c"]
	print(d.values())                  # [1, 2, 3]
	print(d.has("a"))                  # true
	print(d.has_all(["a", "b"]))       # true
	print(d.get("z", -1))              # -1
	print(d.find_key(2))               # b   (the first key with that value)
	print("a" in d)                    # true (the "in" operator checks keys)
```

### `merge()`

`merge(other, overwrite = false)` copies all pairs from `other` into the dictionary. By default existing keys are **kept**. Pass `true` to let the new values replace them:

```gdscript
extends Node


func _ready() -> void:
	var defaults := {"volume": 5, "fullscreen": false}
	var settings := {"volume": 8}

	settings.merge(defaults)           # Adds missing keys, keeps volume = 8
	print(settings)                    # { "volume": 8, "fullscreen": false }

	settings.merge({"volume": 1}, true)   # overwrite = true
	print(settings)                    # { "volume": 1, "fullscreen": false }
```

`merged(other, overwrite = false)` does the same but returns a **new** dictionary and leaves the original unchanged.

### Copying

Just like arrays, assigning a dictionary does not copy it. Use `duplicate()` (shallow) or `duplicate(true)` (deep):

```gdscript
extends Node


func _ready() -> void:
	var a := {"stats": {"hp": 10}}
	var shallow := a.duplicate()
	var deep := a.duplicate(true)

	a["stats"]["hp"] = 99
	print(shallow["stats"]["hp"])    # 99  (shares the inner dictionary)
	print(deep["stats"]["hp"])       # 10  (independent copy)
```

### Sorting

Dictionaries can be sorted by keys with `dict.keys()` and `sort()`:

```gdscript
extends Node


func _ready() -> void:
	var scores := {"Cy": 70, "Ada": 90, "Bob": 50}
	var names := scores.keys()
	names.sort()
	for n in names:
		print(n, ": ", scores[n])    # Ada, Bob, Cy
```

---

## 14.6 Nested Dictionaries

Values can be other dictionaries or arrays, which lets you model structured data:

```gdscript
extends Node


func _ready() -> void:
	var game := {
		"player": {
			"name": "Ada",
			"position": Vector2(10, 20),
			"inventory": ["sword", "potion"],
		},
		"settings": {
			"volume": 7,
			"controls": {"jump": "Space", "attack": "Z"},
		},
	}

	print(game["player"]["name"])                 # Ada
	print(game["player"]["inventory"][0])         # sword
	print(game["settings"]["controls"]["jump"])   # Space
	print(game.player.inventory.size())           # 2

	game["player"]["inventory"].append("shield")
	game["settings"]["volume"] = 3
```

### Reading nested data safely

Each step can fail if a key is missing. Use `get()` with defaults, or check with `has()`:

```gdscript
extends Node


func _ready() -> void:
	var game := {"player": {"name": "Ada"}}

	var inventory = game.get("player", {}).get("inventory", [])
	print(inventory)          # []

	if game.has("player") and game["player"].has("name"):
		print(game["player"]["name"])
```

Nested dictionaries are what you get when you parse JSON ([Lesson 37](./[37]-Saving-and-Loading.md)). When the nesting becomes deep and you use the same shape repeatedly, consider a **custom class or Resource** instead ([Lesson 23](./[23]-Custom-Resources.md)).

---

## 14.7 Typed Dictionaries

From **Godot 4.4**, dictionaries can declare the type of their keys and values:

```gdscript
extends Node


func _ready() -> void:
	var scores: Dictionary[String, int] = {"Ada": 90, "Bob": 70}
	var tiles: Dictionary[Vector2i, String] = {Vector2i(0, 0): "grass"}

	scores["Cy"] = 55                # OK
	# scores["Dee"] = "high"         # ERROR: the value must be an int
	# scores[5] = 10                 # ERROR: the key must be a String
	print(scores, tiles)
```

Benefits:

- The editor catches wrong key and value types.
- Auto-completion works for values you read out.
- Slightly better performance and clearer intent.

You can type only what you need by using `Variant` for the other part, for example `Dictionary[String, Variant]`. Typed dictionaries can be used as parameter and return types:

```gdscript
extends Node


func count_letters(text: String) -> Dictionary[String, int]:
	var result: Dictionary[String, int] = {}
	for letter in text:
		result[letter] = result.get(letter, 0) + 1
	return result


func _ready() -> void:
	print(count_letters("hello"))    # { "h": 1, "e": 1, "l": 2, "o": 1 }
```

If your project must work in Godot 4.0 to 4.3, use a plain `Dictionary` and convert values with explicit types when you read them: `var hp: int = stats["hp"]`.

---

## 14.8 Dictionaries as Lightweight Records

A **record** is a bundle of related fields, such as the data for one enemy. A dictionary is a quick way to create one without writing a class.

```gdscript
extends Node

var enemies: Array[Dictionary] = []


func add_enemy(enemy_name: String, hp: int, speed: float) -> void:
	enemies.append({
		"name": enemy_name,
		"hp": hp,
		"speed": speed,
		"alive": true,
	})


func damage_all(amount: int) -> void:
	for enemy in enemies:
		enemy["hp"] -= amount
		if enemy["hp"] <= 0:
			enemy["alive"] = false


func _ready() -> void:
	add_enemy("Slime", 10, 40.0)
	add_enemy("Bat", 6, 120.0)
	damage_all(8)

	for enemy in enemies:
		print(enemy["name"], " alive: ", enemy["alive"])
```

Output:

```
Slime alive: true
Bat alive: false
```

### A lookup table

Dictionaries make excellent lookup tables that replace long `if`/`elif` chains:

```gdscript
extends Node

const DAMAGE_BY_WEAPON := {
	"dagger": 4,
	"sword": 8,
	"axe": 12,
}


func get_damage(weapon: String) -> int:
	return DAMAGE_BY_WEAPON.get(weapon, 1)


func _ready() -> void:
	print(get_damage("sword"))    # 8
	print(get_damage("stick"))    # 1
```

### When to prefer something else

Dictionaries are flexible, but that comes at a cost:

- A **typo in a key** (`"helth"`) is only found when that line runs.
- The editor cannot auto-complete field names or check their types.
- There is no place to attach behavior (functions) to the data.

For data with a fixed shape that is used throughout your game (items, enemies, characters), a **custom class** or a **custom `Resource`** gives you auto-completion, type checking, and editor integration ([Lesson 18](./[18]-Classes-and-Scripts.md) and [Lesson 23](./[23]-Custom-Resources.md)). Use dictionaries for quick prototypes, JSON data, and truly dynamic key-value data.

---

[Previous](./[13]-Arrays.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[15]-Enums-and-Constants.md)