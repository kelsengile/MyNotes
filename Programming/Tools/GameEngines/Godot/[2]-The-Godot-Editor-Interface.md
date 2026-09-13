[Previous](./[1]-Installing-And-Setting-Up-Godot.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[3]-Nodes-And-The-Scene-Tree.md)

*Getting Started*

# Lesson 2 - The Godot Editor Interface

## 2.1 The Main Editor Layout (Viewport, Scene Dock, FileSystem, Inspector)

When you open a project, the editor is divided into a handful of docks arranged around a central viewport:

```
┌───────────────┬───────────────────────────────┬───────────────┐
│               │                                │               │
│  Scene Dock   │                                │   Inspector   │
│ (node tree)   │           Viewport              │ (properties)  │
│               │        (2D / 3D canvas)         │               │
│               │                                │               │
├───────────────┤                                │               │
│  FileSystem   │                                │               │
│  (project     │                                │               │
│   files)      │                                │               │
├───────────────┴───────────────────────────────┴───────────────┤
│           Bottom Panel (Output, Debugger, Animation...)        │
└──────────────────────────────────────────────────────────────┘
```

- **Scene Dock** (top-left) — shows the **scene tree**: the nodes that make up the scene you currently have open, nested to show parent/child relationships.
- **FileSystem Dock** (bottom-left) — a file browser scoped to your project folder. Every script, scene, image, and sound you add to the project shows up here.
- **Viewport** (center) — the visual canvas where you position and preview your scene. Switches between 2D and 3D depending on the scene type.
- **Inspector** (right) — shows every editable property of whichever node is currently selected in the Scene Dock. This is where you set things like a sprite's texture, a light's color, or a button's text.

These four panels are what you'll interact with constantly, so it's worth building muscle memory for where each one lives.

---

## 2.2 The 2D And 3D Viewports

Godot has two separate viewport modes, switched via tabs at the top of the central panel: **2D** and **3D**. Which one is active depends on the type of scene you have open — a scene rooted at a `Node2D` opens in the 2D viewport, one rooted at a `Node3D` opens in 3D.

**2D Viewport**
- Uses pixel coordinates, with `(0, 0)` at the top-left and Y increasing **downward**.
- Common navigation: scroll wheel to zoom, middle-mouse-drag to pan.

**3D Viewport**
- Uses a right-handed coordinate system with Y pointing **up**.
- Navigation: right-mouse-drag to orbit, `W A S D` + right-click-drag to fly through the scene (similar to many 3D modeling tools), scroll wheel to zoom.
- Includes a small **gizmo** in the corner showing your current orientation, and on-screen move/rotate/scale handles for the selected object.

You'll spend most of a 2D game's development time in the 2D viewport and most of a 3D game's time in the 3D one, but nothing stops a project from using both — for example, a 3D game with a 2D HUD layered on top via a `CanvasLayer`.

---

## 2.3 The Bottom Panels (Output, Debugger, Animation)

The bottom panel is tabbed and context-sensitive — different tabs appear depending on what you're doing:

- **Output** — shows `print()` statements and general logging. This is your first stop when something isn't behaving as expected.
- **Debugger** — appears automatically when your game hits an error or a breakpoint while running. Shows the call stack, active scene tree, and variable values at the moment of the error.
- **Animation** — opens when you're editing an `AnimationPlayer`, showing a timeline of keyframes and tracks.
- **Audio** — shows active audio buses and lets you adjust volume/effects in real time.

You can run your project at any time with the **Play** button (▶) in the top-right corner of the editor, or **Play Current Scene** (the icon next to it) to test just the scene you're editing rather than the project's designated main scene.

---

## 2.4 Customizing The Workspace

Every dock in Godot can be dragged, resized, or torn off into a floating window — useful if you're working on a second monitor. A few adjustments that most people make early on:

- **Distraction Free Mode** (`Shift+F11` by default) hides every panel except the viewport — handy when you just want to look at your scene.
- Dock **tabs can be merged**: drag one dock's tab onto another to combine them, freeing up screen space.
- The **Editor Settings** menu (`Editor > Editor Settings`) lets you change the theme, font size, and keybindings globally, separate from any one project's settings.
- **Project Settings** (`Project > Project Settings`) is different from Editor Settings — it controls settings specific to the project you have open, like the display resolution, input map, and physics layers, all of which we'll use in later lessons.

Getting comfortable rearranging the workspace now will pay off once your scenes and scripts get more complex — there's no one "correct" layout, only the one that keeps your most-used panels visible.

[Previous](./[1]-Installing-And-Setting-Up-Godot.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[3]-Nodes-And-The-Scene-Tree.md)
