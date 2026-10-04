[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[12]-Error-Handling.md)

*Core Syntax*

# Lesson 11 - String Formatting & Manipulation

Text is everywhere in games: dialogue, scores, menus, file paths, and chat messages. This lesson shows you how to build strings from other values, cut them apart, search them, and clean them up.

Unless noted otherwise, the examples assume they are placed inside a function such as `_ready()` in a script that starts with `extends Node`.

---

## 11.1 The `%` Format Operator

The `%` operator inserts values into a string at **placeholders**. The placeholders start with `%` and end with a letter that says what kind of value goes there.

```gdscript
extends Node


func _ready() -> void:
	var name := "Ada"
	var level := 7
	var health := 82.5

	print("Player: %s" % name)                        # Player: Ada
	print("Level: %d" % level)                        # Level: 7
	print("Health: %f" % health)                      # Health: 82.500000
	print("%s is level %d" % [name, level])           # Ada is level 7
```

### Common placeholders

| Placeholder | Meaning | Example |
| --- | --- | --- |
| `%s` | Any value as a string | `"%s" % 42` gives `42` |
| `%d` | Integer (decimal) | `"%d" % 42` gives `42` |
| `%f` | Float | `"%f" % 3.5` gives `3.500000` |
| `%x` / `%X` | Hexadecimal | `"%x" % 255` gives `ff` |
| `%o` | Octal | `"%o" % 8` gives `10` |
| `%c` | A character from its code | `"%c" % 65` gives `A` |
| `%%` | A literal percent sign | `"100%%" % []` gives `100%` |

### Controlling width and precision

```gdscript
extends Node


func _ready() -> void:
	print("%.2f" % 3.14159)     # 3.14     (2 decimal places)
	print("%.0f" % 2.7)         # 3
	print("%5d" % 42)           # "   42"  (padded with spaces to width 5)
	print("%05d" % 42)          # 00042    (padded with zeros)
	print("%-5d|" % 42)         # 42   |   (left-aligned)
	print("%+d" % 5)            # +5       (always show the sign)
	print("%8.3f" % 3.14159)    # "   3.142"
	print("%02d:%02d" % [5, 7]) # 05:07
```

### Rules

- With **one** value, you can write it directly. With **several**, pass them in an array `[a, b, c]` in the same order as the placeholders.
- If the number of values does not match the number of placeholders, you get an error.
- To put a literal `%` in a string that is being formatted, write `%%`.
- To format **one array** as a single value, wrap it in another array: `"%s" % [[1, 2, 3]]`.

---

## 11.2 The `.format()` Method

`String.format()` replaces **named placeholders** in curly braces using a dictionary (or an array of pairs). It is easier to read when a string has many parts or when the same value is used more than once.

```gdscript
extends Node


func _ready() -> void:
	var text := "{name} reached level {level}!".format({"name": "Ada", "level": 7})
	print(text)       # Ada reached level 7!

	var again := "{x}, {y}, {x}".format({"x": 1, "y": 2})
	print(again)      # 1, 2, 1
```

You can also use **positional** placeholders with an array: `{0}`, `{1}`, and so on. The braces contain either an index or a name when you use pairs:

```gdscript
extends Node


func _ready() -> void:
	print("{0} + {1} = {2}".format([2, 3, 5]))    # 2 + 3 = 5
	print("{} and {}".format(["A", "B"]))         # A and B (empty braces fill in order)
```

### Which one should I use?

| Use `%` when... | Use `.format()` when... |
| --- | --- |
| You need number formatting (`%.2f`, `%05d`) | You have many values or reuse a value |
| The string is short and simple | Readability and named values matter |
| | Strings come from translations, where order may change |

You can combine them, formatting a number first and then placing it into a template:

```gdscript
extends Node


func _ready() -> void:
	var price := 3.5
	var formatted := "%.2f" % price
	print("Total: ${amount}".format({"amount": formatted}))   # Total: $3.50
```

---

## 11.3 Converting with `str()` and `String.num()`

### `str()`

`str()` converts almost any value to a string. With several arguments, it joins them together:

```gdscript
extends Node


func _ready() -> void:
	print(str(42))                       # 42
	print(str(3.14))                     # 3.14
	print(str(true))                     # true
	print(str(Vector2(1, 2)))            # (1, 2)
	print(str([1, 2, 3]))                # [1, 2, 3]
	print(str("Score: ", 100, "!"))      # Score: 100!
	print("Lives: " + str(3))            # Lives: 3
```

### `String.num()` and friends

When you need control over how a number is converted:

```gdscript
extends Node


func _ready() -> void:
	print(String.num(3.14159, 2))       # 3.14   (limit decimals)
	print(String.num(5.0))              # 5
	print(String.num_int64(255, 16))    # ff     (any base from 2 to 36)
	print(String.num_scientific(12345.0))   # 12345
	print(String.num(1.0 / 3.0, 4))     # 0.3333
```

### Converting strings back to numbers

```gdscript
extends Node


func _ready() -> void:
	print("42".to_int())          # 42
	print("3.5".to_float())       # 3.5
	print("abc".to_int())         # 0   (no error: invalid text becomes 0)
	print("42".is_valid_int())    # true
	print("4.2".is_valid_int())   # false
	print("4.2".is_valid_float()) # true
	print("0xFF".hex_to_int())    # 255
```

Use `is_valid_int()` and `is_valid_float()` to check player input **before** converting.

---

## 11.4 String Slicing and Indexing

### Indexing

A string is a sequence of characters. Use `[index]` to get one. Counting starts at `0`, and negative indexes count from the end:

```gdscript
extends Node


func _ready() -> void:
	var word := "Godot"
	print(word[0])      # G
	print(word[4])      # t
	print(word[-1])     # t
	print(word[-2])     # o
	print(word.length()) # 5
```

Going outside the valid range is an error, so check `length()` first when unsure.

### Slicing with `substr()` and `left()` / `right()`

```gdscript
extends Node


func _ready() -> void:
	var text := "Hello, World"
	print(text.substr(7, 5))     # World  (start at 7, take 5 characters)
	print(text.substr(7))        # World  (everything from 7)
	print(text.left(5))          # Hello  (first 5 characters)
	print(text.right(5))         # World  (last 5 characters)
	print(text.left(-7))         # Hello  (all except the last 7)
	print(text.right(-7))        # World  (all except the first 7)
```

Strings also support the slice method `String.get_slice(delimiter, index)`, which splits and returns one part:

```gdscript
extends Node


func _ready() -> void:
	var path := "res://assets/hero.png"
	print(path.get_slice("/", 2))        # assets
	print(path.get_file())               # hero.png
	print(path.get_extension())          # png
	print(path.get_basename())           # res://assets/hero
	print(path.get_base_dir())           # res://assets
```

### Searching

```gdscript
extends Node


func _ready() -> void:
	var s := "banana"
	print(s.find("an"))              # 1   (first position, or -1 if not found)
	print(s.rfind("an"))             # 3   (search from the end)
	print(s.find("xyz"))             # -1
	print(s.count("a"))              # 3
	print(s.contains("nan"))         # true
	print(s.begins_with("ban"))      # true
	print(s.ends_with("na"))         # true
```

---

## 11.5 Common String Methods (`split`, `join`, `replace`, `find`, `to_upper`, `strip_edges`)

Remember that string methods **return a new string**. They never change the original.

### `split()` and `join()`

```gdscript
extends Node


func _ready() -> void:
	var csv := "red,green,blue"
	var colors := csv.split(",")           # A PackedStringArray
	print(colors)                          # ["red", "green", "blue"]
	print(colors[1])                       # green

	var words := "the quick brown fox".split(" ")
	print(words.size())                    # 4

	var rejoined := " - ".join(colors)     # Note: the separator is the string you call join on
	print(rejoined)                        # red - green - blue

	print("a,,b".split(","))               # ["a", "", "b"]
	print("a,,b".split(",", false))        # ["a", "b"]  (skip empty parts)
```

### `replace()`

```gdscript
extends Node


func _ready() -> void:
	var s := "I like cats. Cats are great."
	print(s.replace("cats", "dogs"))       # I like dogs. Cats are great.  (case-sensitive)
	print(s.replacen("cats", "dogs"))      # I like dogs. dogs are great.  (ignores case)
	print("a-b-c".replace("-", ""))        # abc
```

### Changing case

```gdscript
extends Node


func _ready() -> void:
	print("Hello".to_upper())             # HELLO
	print("Hello".to_lower())             # hello
	print("hello world".capitalize())     # Hello World
	print("PlayerName".to_snake_case())   # player_name
	print("player_name".to_pascal_case()) # PlayerName
	print("player_name".to_camel_case())  # playerName
```

### Trimming whitespace

```gdscript
extends Node


func _ready() -> void:
	var raw := "   hello   \n"
	print("[" + raw.strip_edges() + "]")                # [hello]
	print("[" + raw.strip_edges(true, false) + "]")     # [hello   \n]  (left only)
	print("[" + raw.strip_edges(false, true) + "]")     # [   hello]    (right only)
	print("xxhixx".trim_prefix("xx"))                   # hixx
	print("xxhixx".trim_suffix("xx"))                   # xxhi
```

### Other useful methods

```gdscript
extends Node


func _ready() -> void:
	print("hello".is_empty())             # false
	print("".is_empty())                  # true
	print("5".pad_zeros(3))               # 005
	print("hi".lpad(5, "."))              # ...hi  (pad on the left)
	print("hi".rpad(5, "."))              # hi...  (pad on the right)
	print("Hello".unicode_at(0))          # 72
	print(String.chr(72))                 # H
	print("test".hash())                  # a number that identifies the text
```

> **Note:** GDScript has no built-in `reverse()` for strings. To reverse one, loop over it from the end, or split it into characters, reverse the array, and join it again.

---

## 11.6 Multiline Strings and Escape Characters

### Multiline strings

Use **triple quotes** to write a string across multiple lines. New lines inside it are kept:

```gdscript
extends Node


func _ready() -> void:
	var poem := """Roses are red,
Violets are blue,
GDScript is fun,
And so are you."""
	print(poem)
```

Inside the triple quotes, indentation is part of the string, so lines that are indented in the code will also be indented in the text.

You can also continue a long line of code with a **backslash** at the end of the line. This does not add a line break to the text:

```gdscript
extends Node


func _ready() -> void:
	var long_text := "This is a very long sentence that " + \
		"continues on the next line of code."
	print(long_text)
```

### Escape characters

An **escape sequence** is a backslash followed by a character. It stands for something that is hard to type directly:

| Sequence | Meaning |
| --- | --- |
| `\n` | New line |
| `\t` | Tab |
| `\\` | A backslash |
| `\"` | A double quote |
| `\'` | A single quote |
| `\r` | Carriage return |
| `\uXXXX` | A Unicode character (4 hex digits) |
| `\UXXXXXX` | A Unicode character (6 hex digits) |

```gdscript
extends Node


func _ready() -> void:
	print("Line one\nLine two")
	print("Name:\tAda")
	print("She said \"Hello\"")
	print("C:\\Users\\Ada")
	print("Heart: \u2665")
	print("Copyright: \u00A9")
```

Output:

```
Line one
Line two
Name:	Ada
She said "Hello"
C:\Users\Ada
Heart: ♥
Copyright: ©
```

---

## 11.7 Raw Strings and `tr()` for Translation

### Raw strings

A **raw string** is written with an `r` before the opening quote. Backslashes are **not** treated as escape sequences. This is handy for file paths and regular expressions:

```gdscript
extends Node


func _ready() -> void:
	print(r"C:\Users\Ada\notes.txt")    # C:\Users\Ada\notes.txt
	print(r"\d+\.\d+")                  # \d+\.\d+
	print("C:\\Users\\Ada")             # The normal-string equivalent needs doubled backslashes
```

Raw strings still need a way to include the quote character. A backslash before a quote is kept in the string, so you can write `r"She said \"hi\""` and the output includes the backslashes.

### `tr()` for translation

`tr()` looks up a string in your project's **translation tables** and returns the version in the player's language. If no translation exists, the original text is returned.

```gdscript
extends Node


func _ready() -> void:
	print(tr("START_GAME"))       # "Start Game" in English, "Iniciar juego" in Spanish, ...
	print(tr("QUIT"))
```

The key (`"START_GAME"`) is a **message ID**. You supply translations in:

1. A **CSV file** with one column per language (the easiest way). Importing it creates `.translation` files.
2. A **gettext** `.po` file (popular with professional translators).

Register the translation files in **Project Settings > Localization > Translations**. Then switch language at runtime:

```gdscript
extends Node


func _ready() -> void:
	TranslationServer.set_locale("es")
	print(tr("START_GAME"))
```

Tips:

- `Label`, `Button`, and other `Control` nodes **translate their text automatically** by default if the text matches a key.
- Use `tr()` for strings you build in code, and combine it with formatting: `tr("WELCOME_MSG").format({"name": player_name})`.
- `tr_n()` handles plural forms (for example "1 coin" versus "2 coins").
- Do not build sentences by joining translated fragments, because word order differs between languages. Translate the **whole sentence** with placeholders.

---

[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[12]-Error-Handling.md)