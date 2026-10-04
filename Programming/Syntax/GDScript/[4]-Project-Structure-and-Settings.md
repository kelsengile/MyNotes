[Previous](./[3]-Running-GDScript-Code.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[5]-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 4 - Project Structure & Settings

A Godot project is just a folder full of files, but how you organize that folder and how you configure the project settings will affect you for the whole life of your game. This lesson covers the special paths Godot uses, the project file, a sensible folder layout, the most important settings, how assets are imported, and how to use Git.

---

## 4.1 `res://` and `user://` Paths

Godot uses two special path prefixes so that your game works the same on every operating system.

### `res://` (the resource path)

`res://` points to the **root of your project folder**, the folder that contains `project.godot`.

```gdscript
extends Node


func _ready() -> void:
	var texture := load("res://icon.svg")
	var scene := load("res://scenes/player.tscn")
	print(texture)
```

Facts about `res://`:

- It is the same on every computer. `res://icon.svg` always means "the `icon.svg` in the project root".
- It is **read-only in an exported game.** Your files are packed into an archive, so you cannot save to `res://` once the game is exported.
- Always use forward slashes (`/`), even on Windows.

### `user://` (the user data path)

`user://` points to a **writable folder** reserved for your game's data on the player's computer. It is where you store save files, settings, and logs.

```gdscript
extends Node


func _ready() -> void:
	# Prints the real, operating-system-specific location of user://
	print(ProjectSettings.globalize_path("user://"))
```

Typical real locations (the folder name comes from your project name):

| OS | Location |
| --- | --- |
| Windows | `%APPDATA%\Godot\app_userdata\<project name>\` |
| macOS | `~/Library/Application Support/Godot/app_userdata/<project name>/` |
| Linux | `~/.local/share/godot/app_userdata/<project name>/` |

You can open this folder from the editor with **Project > Open User Data Folder**. You can also define a custom folder name in **Project Settings > Application > Config > Use Custom User Dir**.

### Quick comparison

| | `res://` | `user://` |
| --- | --- | --- |
| Contains | Your game's assets and scripts | Save files, settings, logs |
| Writable in an exported game? | No | Yes |
| Shipped with the game? | Yes | No, created on the player's computer |

---

## 4.2 The `project.godot` File

The file `project.godot` in the project root defines the folder as a Godot project. It is a plain-text file in an INI-like format. You usually change it through **Project Settings** in the editor, but you can open it in a text editor to see what is inside:

```ini
; Engine configuration file.
config_version=5

[application]

config/name="HelloGodot"
run/main_scene="res://main.tscn"
config/features=PackedStringArray("4.4", "Forward Plus")
config/icon="res://icon.svg"

[display]

window/size/viewport_width=1280
window/size/viewport_height=720

[input]

jump={
"deadzone": 0.2,
"events": [Object(InputEventKey,"keycode":32)]
}

[autoload]

GameState="*res://game_state.gd"
```

Sections you will see often:

| Section | What it holds |
| --- | --- |
| `[application]` | Project name, icon, main scene |
| `[display]` | Window size, stretch mode, fullscreen |
| `[input]` | Your custom input actions |
| `[autoload]` | Globally loaded scripts and scenes |
| `[layer_names]` | Names for physics and render layers |
| `[rendering]` | Renderer and quality settings |

Important notes:

- **Do not delete** `project.godot`, because the project would no longer be recognized.
- Because it is a text file, it works well with version control, and you can see exactly what changed in a commit.
- Only settings that differ from the defaults are stored in it. That keeps the file short.

You can read and change settings from code with the `ProjectSettings` singleton:

```gdscript
extends Node


func _ready() -> void:
	var width: int = ProjectSettings.get_setting("display/window/size/viewport_width")
	print("Viewport width: ", width)
```

---

## 4.3 Organizing Folders, Scenes, and Scripts

Godot does not force a folder structure on you. Still, a clear layout makes projects easier to maintain. There are two popular approaches.

### Approach A: Group by type

```
res://
├── assets/
│   ├── art/
│   ├── audio/
│   └── fonts/
├── scenes/
│   ├── player.tscn
│   └── enemy.tscn
├── scripts/
│   ├── player.gd
│   └── enemy.gd
└── project.godot
```

Simple, and fine for small projects and while you are learning.

### Approach B: Group by feature (recommended for larger projects)

```
res://
├── characters/
│   ├── player/
│   │   ├── player.tscn
│   │   ├── player.gd
│   │   └── player.png
│   └── enemy/
│       ├── enemy.tscn
│       └── enemy.gd
├── ui/
│   ├── main_menu.tscn
│   └── main_menu.gd
├── levels/
├── autoload/
└── project.godot
```

Everything that belongs to the player lives together, so the `player` folder can be copied or deleted as a unit.

### Naming conventions

The official style guide recommends:

- **Folders and files:** `snake_case` (for example `main_menu.tscn`, `player_stats.gd`). This avoids problems on case-sensitive systems such as Linux, where `Player.png` and `player.png` are different files.
- **Node names inside scenes:** `PascalCase` (for example `Player`, `Sprite2D`, `HealthBar`).
- **Script name matches scene name:** `player.tscn` has `player.gd`.

### Good habits

- Keep the **script next to its scene**.
- Do not use spaces or special characters in file names.
- Use the **FileSystem dock** (not the OS) to rename and move files, so references stay valid.
- Keep third-party add-ons in the `addons/` folder, since Godot treats that folder specially.

---

## 4.4 Project Settings (Display, Input Map, Layers, Autoloads)

Open **Project > Project Settings**. Turn on **Advanced Settings** (top right of the dialog) to see everything. The most important areas for you are:

### Display

Found under **Display > Window**.

- **Viewport Width / Height:** The base resolution of your game, for example 1152 x 648 or 1920 x 1080.
- **Stretch > Mode:** How the game scales when the window is resized. `canvas_items` is good for 2D and UI. `viewport` is good for pixel art.
- **Stretch > Aspect:** How the game handles different aspect ratios (`keep`, `expand`, etc.).
- **Mode:** Windowed, fullscreen, and so on.

### Input Map

Found in the **Input Map** tab. Here you define **actions** such as `jump` or `move_left` and bind keys, mouse buttons, or gamepad buttons to them. Then, in code:

```gdscript
extends Node


func _process(_delta: float) -> void:
	if Input.is_action_just_pressed("jump"):
		print("Jump!")
```

Using actions instead of hard-coded keys lets players remap controls. Input is covered in [Lesson 28](./[28]-Input-Handling.md).

### Layer Names

Found under **Layer Names**. You can give meaningful names to **2D/3D physics layers**, **render layers**, and **navigation layers** (for example, layer 1 = `world`, layer 2 = `player`, layer 3 = `enemies`). Names appear in the Inspector's checkboxes, which makes collision setup much easier to read. See [Lesson 32](./[32]-2D-Physics-and-Movement.md).

### Autoload

Found in the **Globals** tab (or **Autoload** tab in older 4.x versions). Here you register scripts or scenes that Godot loads automatically at startup and keeps alive for the whole game. These are used for global state such as score or settings. See [Lesson 30](./[30]-Groups-and-Autoloads.md).

### Other useful settings

- **Application > Config > Name / Icon:** Your game's title and icon.
- **Application > Run > Main Scene:** The scene started by `F5`.
- **Debug > GDScript:** Warning levels, including typing-related warnings (see [Lesson 17](./[17]-Static-Typing.md)).
- **Physics > Common > Physics Ticks per Second:** How often `_physics_process()` runs (60 by default).

---

## 4.5 Importing Assets (Images, Audio, 3D Models)

To use an asset, simply **drag the file into your project** (ideally via the FileSystem dock). Godot **imports** it automatically.

### What happens during import

1. You copy `hero.png` into the project.
2. Godot creates a hidden converted copy inside the `.godot/imported/` folder and a small `hero.png.import` file next to the original.
3. The `.import` file stores the **import settings**. Your original file is never changed.

Always commit `.import` files to version control, since they hold your settings. Never edit or commit the `.godot/imported/` folder contents.

### Changing import settings

Click the file in the FileSystem dock, open the **Import** tab (next to the Scene dock), change settings, and click **Reimport**.

### Common asset types

| Type | Formats | Notes |
| --- | --- | --- |
| **Images** | PNG, JPG, WebP, SVG | For **pixel art**, set **Filter** to *Nearest* in Project Settings (**Rendering > Textures > Canvas Textures > Default Texture Filter**) to keep it crisp |
| **Audio** | WAV, OGG Vorbis, MP3 | WAV for short sound effects. OGG for music. Set **Loop** in the import tab for looping music |
| **3D models** | glTF 2.0 (`.glb`, `.gltf`), FBX, OBJ, Blend | glTF is the recommended format |
| **Fonts** | TTF, OTF, WOFF | Used in `Label` and other UI controls |
| **Data** | CSV, JSON, text | Loaded with `FileAccess` or imported as translations |

Use the assets in code with `load()` or `preload()`:

```gdscript
extends Sprite2D


func _ready() -> void:
	texture = preload("res://assets/art/hero.png")
```

> **Tip:** If an imported asset looks blurry, wrong, or out of date, select it and click **Reimport**. If many things look wrong, delete the `.godot/` folder. Godot will rebuild it the next time you open the project.

---

## 4.6 Version Control with Git (`.gitignore`, `.godot/` folder)

Godot projects work very well with **Git** because scenes and resources are saved as readable text.

### Enabling Git support

When you create a project, set **Version Control Metadata** to **Git**. Godot generates two helper files:

- `.gitignore`: tells Git what **not** to track
- `.gitattributes`: normalizes line endings

If you forgot, use **Project > Version Control > Generate Version Control Metadata**.

### What the `.gitignore` contains

The standard Godot `.gitignore` is short:

```
# Godot 4+ specific ignores
.godot/

# Godot-specific ignores
*.translation
```

Depending on your needs, you might also ignore export folders:

```
export/
builds/
```

### What to commit and what to ignore

| Commit (track) | Do **not** commit (ignore) |
| --- | --- |
| `project.godot` | `.godot/` (cache, regenerated automatically) |
| `.gd` scripts | Exported builds |
| `.tscn` and `.tres` files | `export_credentials.cfg` (may contain secrets) |
| Original assets and their `.import` files | Temporary files |
| `.gitignore` and `.gitattributes` | |
| `export_presets.cfg` (without secrets) | |

### Basic Git workflow

```bash
git init
git add .
git commit -m "Initial Godot project"
```

Tips:

- Commit **small, frequent** changes with clear messages.
- Close scenes you are not editing before committing, so unwanted automatic changes are not saved.
- Use **branches** for experimental features.
- Large binary files (big audio or 3D models) can be handled with [Git LFS](https://git-lfs.com/).

The `.godot/` folder is purely a cache. If it is deleted, Godot recreates it, reimporting your assets, which can take a moment on large projects.

---

[Previous](./[3]-Running-GDScript-Code.md) | [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[5]-Variables-and-Data-Types.md)