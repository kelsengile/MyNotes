[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[3]-Running-GDScript-Code.md)

*Getting Started*

# Lesson 2 - The Godot Editor Tour

The Godot editor is where you build scenes, write scripts, test your game, and debug problems. It can look crowded the first time you open it, but it is organized into a small number of areas that each do one job. This lesson gives you a map so that you always know where to find things.

---

## 2.1 The Main Workspaces (2D, 3D, Script, AssetLib)

At the very top-center of the editor are the **main workspace buttons**. Each one switches the large central area to a different tool:

| Workspace | What it is for |
| --- | --- |
| **2D** | Arranging 2D nodes (sprites, UI, tilemaps) on a flat canvas |
| **3D** | Arranging 3D nodes (meshes, lights, cameras) in a 3D viewport |
| **Script** | Writing and editing code with the built-in script editor |
| **Game** | Running and interacting with your game inside the editor window (Godot 4.4+) |
| **AssetLib** | Browsing and downloading free assets and add-ons |

Some tips for moving around:

- You can switch workspaces with the shortcuts `Ctrl+F1` (2D), `Ctrl+F2` (3D), `Ctrl+F3` (Script), and so on (`Cmd` instead of `Ctrl` on macOS).
- When you select a 2D node such as `Sprite2D`, Godot jumps to the **2D** workspace automatically. Selecting a `MeshInstance3D` jumps to **3D**.
- The **2D** workspace is also used for editing **user interfaces**, because `Control` nodes are 2D nodes.

At the top-right you will find the **Play buttons** (Run Project, Pause, Stop, Run Current Scene, Run Specific Scene) and the **renderer selector**. At the very top-left are the **menus**: *Scene, Project, Debug, Editor,* and *Help*.

---

## 2.2 The Scene Dock and Node Dock

By default, the **Scene dock** is in the top-left of the editor. It shows the **tree of nodes** that make up the scene you are editing.

- Each line is a **node**. Indentation shows parent-child relationships.
- The top line is the **root node** of the scene.
- Icons beside the nodes show their types and whether a script is attached (a small scroll icon).
- Click the **+ (Add Child Node)** button to add a new node, or the **chain** button to **instance** another scene.
- Right-click a node to rename, duplicate, delete, attach a script, or change its type.
- Use the **eye icon** to hide nodes in the editor and the **lock icon** to stop them from being selected.

Next to the Scene dock tab is the **Import** tab, used for settings of imported assets.

On the right side, next to the Inspector, there is the **Node dock**. It has two tabs:

- **Signals:** Lists every signal the selected node can emit. Double-click a signal to connect it to a function (covered in [Lesson 26](./[26]-Signals.md)).
- **Groups:** Lets you add the selected node to named groups (covered in [Lesson 30](./[30]-Groups-and-Autoloads.md)).

You will use the Scene dock constantly. Everything in Godot is a node, and the Scene dock is your window into the node tree.

---

## 2.3 The Inspector

The **Inspector** sits on the right side of the editor. It shows every **property** of the node (or resource) you currently have selected.

For example, select a `Sprite2D` and you will see:

- **Texture:** which image it displays
- **Transform > Position, Rotation, Scale:** where it is and how it is oriented
- **Visibility > Visible:** whether it is drawn
- **Script:** the script attached to the node

Useful Inspector features:

- **Edit values directly.** Typing a number, dragging a slider, or picking a color changes the property instantly.
- **Reset button:** A small circular arrow appears when a property differs from its default. Click it to reset.
- **Sections** are collapsible. Click a header to expand or collapse it.
- **Search bar** at the top filters properties by name.
- **Resources** (such as a texture or a shape) can be saved, loaded, or edited inline by clicking the resource field.
- **Exported variables** from your own scripts appear here too. When you write `@export var speed = 200` in a script, a **Speed** field appears in the Inspector (see [Lesson 27](./[27]-Annotations.md)).
- The **history arrows** at the top let you jump back to previously inspected objects.

> **Remember:** A value set in the Inspector is stored in the scene file. A value set in code at runtime is temporary and is lost when the game stops.

---

## 2.4 The FileSystem Dock

The **FileSystem dock**, in the bottom-left, shows every file in your project folder, just like a file explorer.

- All paths start with `res://`, which stands for the project's root folder (see [Lesson 4](./[4]-Project-Structure-and-Settings.md)).
- **Double-click** a scene (`.tscn`) to open it, or a script (`.gd`) to edit it.
- **Drag and drop** files from the dock into the Scene dock or the viewport. Dragging a scene into the viewport instances it. Dragging an image creates a `Sprite2D`.
- **Right-click** to create new folders, scenes, scripts, or resources, and to rename, move, or delete files. Always use the dock to rename or move files, because Godot updates references automatically. Renaming through your operating system's file explorer can break links.
- **Copy the path** of any file using right-click, then **Copy Path** to paste it into code.
- Use the **filter box** to search files by name.

Common file types you will see:

| Extension | Meaning |
| --- | --- |
| `.gd` | GDScript file |
| `.tscn` | Text-based scene |
| `.tres` | Text-based resource |
| `.godot` | Project file (`project.godot`) |
| `.import` | Import settings generated for an asset (auto-created) |

---

## 2.5 The Script Editor and Built-in Docs

Click the **Script** workspace (or double-click any `.gd` file) to open the **script editor**. Its layout is:

- **Script list** (left): all the scripts currently open. Below it is a list of **methods** in the current script. Click one to jump to it.
- **Code area** (center): where you type.
- **Status bar** (bottom): shows the line and column, indentation type, and any error count.

Helpful features of the code editor:

- **Auto-completion:** Start typing and press `Ctrl+Space` to see suggestions. Press `Tab` or `Enter` to accept one.
- **Syntax highlighting and error underlines:** Red underlines show errors. Yellow ones are warnings.
- **Code folding:** Click the small arrows in the gutter to collapse a function or block.
- **Comment toggle:** `Ctrl+K` (or `Cmd+K`) comments or uncomments the selected lines.
- **Find and replace:** `Ctrl+F` and `Ctrl+R`.
- **Go to line:** `Ctrl+L`.
- **Duplicate line:** `Ctrl+Shift+D`.

### Built-in documentation

Godot ships with the full class reference **offline**. You do not need a browser.

- Press `F1` (or choose **Help > Search Help**) and type any class or method name, such as `Node2D` or `move_and_slide`.
- Hold `Ctrl` and click any class name or method in your code to jump to its documentation.
- Hover over a name in the code editor to see a tooltip.

Developing the habit of looking up classes in the built-in docs is one of the most valuable skills in Godot. Every node, its properties, methods, and signals are all documented there.

---

## 2.6 The Output, Debugger, and Animation Panels

The **bottom panel** of the editor holds several tabs. Click the tab name to open it, and click again to collapse it.

| Panel | Purpose |
| --- | --- |
| **Output** | Shows everything your game prints with `print()`, along with warnings and errors |
| **Debugger** | Shows errors, the call stack, variables, the profiler, and monitors while the game is running |
| **Search Results** | Shows the results of "Find in Files" |
| **Audio** | The audio bus mixer (volume, effects) |
| **Animation** | The timeline editor for `AnimationPlayer` nodes (appears when you select one) |
| **Shader Editor** | Appears when editing a shader |
| **SpriteFrames / TileSet / TileMap** | Specialized editors that appear when you select the matching node |

The two panels you will use the most in this course are:

1. **Output:** `print("Hello")` appears here. You will check it constantly while learning.
2. **Debugger:** When a runtime error happens, the Debugger tab flashes red. It shows you the exact line of the error and the chain of function calls that led there.

You can clear the Output panel with the trash can icon and copy its text with the copy icon. Panels can also be dragged around, floated into separate windows, or resized to fit your screen.

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[3]-Running-GDScript-Code.md)