[Previous](./[7]-Physics-And-Collision-In-Godot.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[9]-Exporting-And-Publishing-A-Godot-Project.md)

*Building A Game*

# Lesson 8 - UI With Control Nodes

## 8.1 The Control Node Family

Every UI element in Godot — buttons, labels, text fields, health bars — inherits from `Control`, the UI equivalent of `Node2D`. Instead of a position/rotation/scale transform, `Control` nodes work in terms of a **rectangle** on screen (position + size), which is what makes anchoring and resizing (covered next) possible.

Common `Control` nodes you'll use constantly:

| Node | Purpose |
|---|---|
| `Label` | Static text |
| `Button` | Clickable button, emits `pressed` |
| `TextureButton` | A button skinned with custom images instead of the default theme |
| `LineEdit` | Single-line text input |
| `TextEdit` | Multi-line text input |
| `ProgressBar` | Health bars, loading bars |
| `TextureRect` | Displays an image within UI (e.g. an icon or portrait) |
| `Panel` / `PanelContainer` | A background box, often used to group other controls |

UI is typically placed under a `CanvasLayer` node so it renders on top of (and independently from) the game world — meaning it won't move if the 2D/3D camera pans or zooms.

```
HUD (CanvasLayer)
├── HealthBar (ProgressBar)
├── ScoreLabel (Label)
└── PauseButton (Button)
```

---

## 8.2 Anchors, Margins, And Containers

Because screens come in wildly different sizes and aspect ratios, `Control` nodes don't just use a fixed pixel position — they use **anchors**: values from 0.0 to 1.0 representing a fraction of the parent's size that the control's edges are pinned to.

- An anchor of `(0, 0)` pins to the top-left corner.
- An anchor of `(1, 1)` pins to the bottom-right corner.
- Setting all four anchors to `1.0` and using negative **margins** (offsets from the anchor) keeps an element a fixed distance from the bottom-right corner regardless of screen size — common for a pause button, for instance.

The Godot editor provides an **Anchor Presets** dropdown in the toolbar (top-left corner, centered, full rect, etc.) so you rarely need to set anchor values by hand.

**Containers** go a step further by automatically arranging their children, removing the need to position anything manually:

| Container | Arranges Children... |
|---|---|
| `VBoxContainer` | Vertically, stacked top to bottom |
| `HBoxContainer` | Horizontally, stacked left to right |
| `GridContainer` | In a grid with a fixed number of columns |
| `CenterContainer` | Centered within the available space |
| `MarginContainer` | With padding around a single child |

```
InventoryPanel (PanelContainer)
└── GridContainer (columns = 4)
    ├── ItemSlot
    ├── ItemSlot
    ├── ItemSlot
    └── ... (repeats as items are added)
```

Because containers reposition their children automatically, you generally shouldn't set a child's position manually inside one — let the container do that job.

---

## 8.3 Building A Simple Menu

A minimal main menu combines containers, buttons, and a script listening for their `pressed` signals:

```
MainMenu (Control, Full Rect anchor)
└── CenterContainer
    └── VBoxContainer
        ├── TitleLabel (Label)
        ├── StartButton (Button)
        ├── OptionsButton (Button)
        └── QuitButton (Button)
```

```gdscript
extends Control

func _ready():
    $CenterContainer/VBoxContainer/StartButton.pressed.connect(_on_start_pressed)
    $CenterContainer/VBoxContainer/QuitButton.pressed.connect(_on_quit_pressed)

func _on_start_pressed():
    get_tree().change_scene_to_file("res://levels/level_1.tscn")

func _on_quit_pressed():
    get_tree().quit()
```

`change_scene_to_file()` swaps the entire current scene tree for a different one — the standard way to move between a menu and gameplay, or between levels. Notice the `$` paths include the full nested route through the containers, matching Lesson 5's signal-connecting pattern exactly — UI communication uses the same signal system as everything else in Godot.

---

## 8.4 Theming UI

Rather than styling every button and label individually, Godot uses **Theme** resources to define reusable, project-wide (or panel-wide) visual styles — fonts, colors, margins, and `StyleBox` backgrounds for each control type.

A `Theme` resource can be:

- Applied globally in **Project Settings > GUI > Theme**, affecting every `Control` in the project by default.
- Applied to a specific `Control` node and its children, overriding the global theme for just that branch.
- Overridden per-node for one-off exceptions, via the **Theme Overrides** section at the bottom of the Inspector when a `Control` node is selected.

A common workflow: build one `Theme` resource with your game's font, button style, and color palette, save it as a `.tres` file, and assign it once at the root of your UI — every `Label`, `Button`, and `ProgressBar` underneath inherits it automatically, keeping the whole game visually consistent without manually styling each individual control.

[Previous](./[7]-Physics-And-Collision-In-Godot.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[9]-Exporting-And-Publishing-A-Godot-Project.md)
