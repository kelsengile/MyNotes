[Previous](./[2]-Editor-Tour.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[4]-Project-Structure-and-Settings.md)

*Getting Started*

# Lesson 3 - Running Code: Scripts, Scenes & the Output Panel

In many languages you write a file and run it from a terminal. Godot works differently: GDScript lives **inside** the engine and is attached to **nodes** in **scenes**. This lesson explains how that fits together, and shows you how to write, attach, and run your very first script.

---

## 3.1 How GDScript Fits into Godot

Three ideas are the foundation of everything in Godot:

- A **node** is the smallest building block: a sprite, a camera, a sound player, a button, a timer, and so on.
- A **scene** is a tree of nodes saved in a file (`.tscn`). A whole level, a player character, or a single button can each be a scene.
- A **script** is a GDScript file (`.gd`) that gives a node custom behavior.

GDScript does not run on its own. Godot loads your scene, builds the nodes, and calls special functions in your scripts at specific moments, for example when the node enters the game (`_ready()`) or on every frame (`_process()`).

```
Scene (Player.tscn)
└── CharacterBody2D      <- script "player.gd" attached here
    ├── Sprite2D
    └── CollisionShape2D
```

Key facts about GDScript:

- It is **indentation-based** (like Python). Blocks of code are defined by how far they are indented, not by braces.
- It is **interpreted** by the engine, so there is no separate compile step. Press play and the code runs.
- Every `.gd` file is a **class**. The file is the class definition, and a node using the script is an instance of it (covered in [Lesson 18](./[18]-Classes-and-Scripts.md)).
- It has **tight integration** with the engine: you call engine features such as `get_node()`, `print()`, or `Input.is_action_pressed()` directly, with no imports.

---

## 3.2 Creating and Attaching a Script to a Node

Let's create a script and attach it to a node.

### Step 1: Create a scene with a root node

1. In the Scene dock, click **Other Node** (or **2D Scene** or **3D Scene**, which pre-creates a root node).
2. For this lesson, choose **Node** (the plainest node type, with no visuals).
3. Save the scene with `Ctrl+S`. Name it `main.tscn`.

### Step 2: Attach a script

1. Select the root node in the Scene dock.
2. Click the **scroll icon with a green plus** (**Attach Script**) at the top of the Scene dock, or right-click the node and choose **Attach Script**.
3. A dialog appears. Its main fields are:
   - **Language:** GDScript
   - **Inherits:** the node type the script extends (automatically `Node` here)
   - **Template:** a starting skeleton. `Node: Default` gives you a basic template; `Empty` gives you a blank file
   - **Path:** where the file will be saved, such as `res://main.gd`
4. Click **Create**. The script editor opens.

A default template looks like this:

```gdscript
extends Node


# Called when the node enters the scene tree for the first time.
func _ready() -> void:
	pass


# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta: float) -> void:
	pass
```

Here is what each piece means:

- `extends Node` says "this script is a `Node`, with all the abilities of a `Node`".
- `func _ready() -> void:` defines a function that Godot calls once when the node is ready.
- `func _process(delta: float) -> void:` defines a function that Godot calls every frame.
- `pass` is a placeholder that does nothing. It is required because a function body cannot be empty.

> **Indentation:** GDScript uses **tabs** by default, and the editor inserts one when you press `Tab`. Never mix tabs and spaces within the same file, or you will get an error.

You can also create a script first (FileSystem dock, right-click, **New > Script...**) and attach it later by dragging it onto a node or by setting the node's **Script** property in the Inspector.

---

## 3.3 Your First Script: `print("Hello, World!")`

Replace the contents of the script with the following:

```gdscript
extends Node


func _ready() -> void:
	print("Hello, World!")
```

What happens here:

1. When the scene starts, the node is added to the game.
2. Godot calls `_ready()` on the node.
3. `print()` sends the text to the **Output panel**.

`print()` accepts any number of arguments and joins them without a separator:

```gdscript
extends Node


func _ready() -> void:
	print("Hello, World!")
	print("Score: ", 100)
	print("A", "B", "C")        # Prints: ABC
	print(1 + 2)                # Prints: 3
	print("Position: ", Vector2(10, 20))
```

Output:

```
Hello, World!
Score: 100
ABC
3
Position: (10, 20)
```

Comments start with `#` and are ignored by the computer. Use them to explain your code.

---

## 3.4 Running the Project, the Current Scene, and the Main Scene

There are three ways to run your game from the editor:

| Action | Button / Shortcut | What it runs |
| --- | --- | --- |
| **Run Project** | `F5` | The **main scene** of the project |
| **Run Current Scene** | `F6` | The scene currently open in the editor |
| **Run Specific Scene** | `Ctrl+Shift+F5` | A scene you choose from a list |

### The main scene

The **main scene** is the scene Godot starts when you export or run the whole project. The first time you press `F5` and no main scene is set, Godot asks you to choose one. You can also set it later in **Project > Project Settings > Application > Run > Main Scene**.

### Stopping

Press `F8` or click the **Stop** button to end the running game. Closing the game window works too.

### Walkthrough

1. Make sure `main.tscn` is open with the script from the previous sub-lesson attached.
2. Press `F6` (Run Current Scene).
3. A game window appears. It is empty because the scene has no visuals.
4. Look at the **Output** panel at the bottom of the editor. You should see `Hello, World!`.

Even though the window is empty, your code ran. For learning GDScript, an empty window plus a message in the Output panel is a perfectly good test setup.

---

## 3.5 Running a Script Directly with `@tool` and `EditorScript`

Sometimes you want to run code **without** starting the game, for example to test a quick calculation or to modify many files at once. Godot offers two ways.

### Option 1: `EditorScript`

An `EditorScript` is a special script that runs once, inside the editor, when you choose **File > Run** (shortcut `Ctrl+Shift+X`) while it is open in the script editor.

```gdscript
@tool
extends EditorScript


func _run() -> void:
	print("This ran inside the editor!")
	print("Godot version: ", Engine.get_version_info().string)
```

How to use it:

1. Create a new script. In the creation dialog, set **Inherits** to `EditorScript`.
2. Write your code in `_run()`.
3. With the script open in the script editor, press `File > Run` (`Ctrl+Shift+X`).
4. The output appears in the Output panel. There is no game window.

This is a great sandbox for experimenting with pure GDScript such as math, strings, arrays, and dictionaries, without building a scene.

### Option 2: `@tool`

The `@tool` annotation at the top of **any** script makes the script run inside the editor as well as in the game:

```gdscript
@tool
extends Node


func _ready() -> void:
	print("I run in the editor too!")
```

With `@tool`, `_ready()` runs when the scene is opened in the editor. Use `Engine.is_editor_hint()` to run different code depending on where the script is running:

```gdscript
@tool
extends Node


func _ready() -> void:
	if Engine.is_editor_hint():
		print("Running in the editor")
	else:
		print("Running in the game")
```

Tool scripts are powerful but can crash or corrupt your scene if they contain bugs, so be careful. They are covered in depth in [Lesson 40](./[40]-Tool-Scripts-and-Plugins.md).

---

## 3.6 Reading Output, Warnings, and Errors

The **Output** panel and the **Debugger** panel both report what your code does. Learning to read them will save you hours.

### Normal output

Text from `print()` appears in the normal color. Use it to check values:

```gdscript
extends Node


func _ready() -> void:
	var health := 100
	print("Health is: ", health)
```

### Warnings

**Yellow** messages are warnings. Your code runs, but something looks suspicious, such as an unused variable:

```gdscript
extends Node


func _ready() -> void:
	var unused := 5   # Warning: The local variable "unused" is declared but never used.
```

### Errors

**Red** messages are errors. There are two kinds:

- **Parse errors (before running):** There is a mistake in the code's structure. The script editor underlines the problem in red, and the game will not start.
- **Runtime errors (while running):** The code is valid but fails when executed, such as accessing something that does not exist. The game may pause, or a function may stop partway.

A typical runtime error message looks like this:

```
E 0:00:00:0123   main.gd:6 @ _ready(): Invalid access to property or key 'name' on a base object of type 'Nil'.
  <GDScript Error>INVALID_GET_INDEX
  <GDScript Source>main.gd:6 @ _ready()
```

How to read it:

1. **`main.gd:6`** is the file and line number where it happened. Click it to jump there.
2. **`_ready()`** is the function that was running.
3. **The message** describes the problem. Here, something is `Nil` (that is, `null`) when you expected a real object.

In the Debugger panel you can also see the **stack trace**, the list of function calls that led to the error, and the **values of variables** at that moment.

> **Tip:** Read error messages from the top, and always start with the line number. More error-reading advice is in [Lesson 12](./[12]-Error-Handling.md).

---

[Previous](./[2]-Editor-Tour.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[4]-Project-Structure-and-Settings.md)