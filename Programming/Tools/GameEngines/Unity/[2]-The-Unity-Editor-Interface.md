[Previous](./[1]-Installing-Unity-And-Unity-Hub.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[3]-GameObjects,-Components,-And-Prefabs-In-Unity.md)

*Getting Started*

# Lesson 2 - The Unity Editor Interface

## 2.1 Scene View vs Game View

Unity splits what would be a single viewport in many engines into two distinct tabs, and mixing them up is a common source of early confusion:

- **Scene View** — your editing workspace. You freely navigate around it, select and move objects, and it never reflects camera framing — you always see everything, regardless of what any in-game Camera is pointed at.
- **Game View** — a preview of exactly what the *player* will see, rendered through your scene's active Camera(s). You don't navigate this view directly; it only changes if the Camera itself moves.

```
┌───────────────────┐   ┌───────────────────┐
│    Scene View       │   │    Game View        │
│  (your editing        │   │  (what the player     │
│   viewport, free       │   │   actually sees,       │
│   navigation)          │   │   through a Camera)     │
└───────────────────┘   └───────────────────┘
```

While the game is running (Play Mode), the Game View updates live, letting you test in real time. Editing GameObjects in the Scene View while in Play Mode is possible but **temporary** — any changes made during Play Mode are discarded once you stop, since you're editing a live runtime copy rather than the saved scene.

---

## 2.2 The Hierarchy And Project Windows

Two panels track two very different things, and the distinction matters:

- **Hierarchy** — lists every GameObject currently in the *open scene*, nested to show parent-child relationships. This is Unity's equivalent of a scene tree.
- **Project** — a file browser for your entire *project's assets on disk* (scripts, textures, prefabs, scenes themselves) regardless of which scene is currently open.

```
Hierarchy (this scene only)      Project (entire project's files)
├── Main Camera                    Assets/
├── Directional Light              ├── Scripts/
├── Player                         ├── Prefabs/
│   ├── Model                      ├── Scenes/
│   └── PlayerController (script)  │   ├── MainMenu.unity
└── Level Geometry                 │   └── Level1.unity
                                    └── Materials/
```

A useful mental model: the **Hierarchy** is "what's alive in this specific scene right now," while the **Project** window is "everything that exists in the project, usable across any scene." Dragging a Prefab (Lesson 3.3) from the Project window into the Hierarchy is how you place a reusable asset into a specific scene.

---

## 2.3 The Inspector

The **Inspector** shows every Component (and its editable fields) attached to whichever GameObject is currently selected in the Hierarchy — Unity's equivalent of Godot's Properties panel, but organized around a stack of Components rather than a single node's own properties (the Component model is covered fully in Lesson 3).

```
Inspector — "Player" GameObject
┌───────────────────────────────┐
│ ▸ Transform                     │
│    Position  (0, 1, 0)          │
│    Rotation  (0, 0, 0)          │
│    Scale     (1, 1, 1)          │
├───────────────────────────────┤
│ ▸ Sprite Renderer                │
│    Sprite    [PlayerIdle]        │
│    Color     [white]             │
├───────────────────────────────┤
│ ▸ PlayerController (Script)      │
│    Speed     5.0                  │
│    Jump Force 8.0                 │
│ [Add Component]                  │
└───────────────────────────────┘
```

Every GameObject always has at least a **Transform** component (position/rotation/scale) — it's the one component that can never be removed, since it's what makes a GameObject exist somewhere in space. Public fields on your own scripts (Lesson 4.2) automatically appear here too, editable without touching code.

---

## 2.4 Layouts And Customization

Like Godot's dockable panels, every Unity window can be dragged, docked, resized, or floated independently — and the **Layout** dropdown (top-right of the editor) lets you save and switch between different arrangements.

Unity ships several built-in layouts (`Default`, `2 by 3`, `Tall`, `Wide`) suited to different workflows — for example, a layout with a larger Scene View for 3D level building, versus one with more room for the Console and Inspector when focused on scripting.

A few adjustments most people make early on:

- **Maximize on Play** (Game tab's overflow menu) auto-expands the Game View to fill the screen while testing, useful for focusing purely on the player experience.
- The **Console window** (Window > General > Console) shows `Debug.Log()` output and compiler errors — functionally identical to Godot's Output panel, and one of the most-used windows once you start scripting in Lesson 4.
- Custom layouts can be saved via **Layout > Save Layout**, letting you build a personal setup and return to it later, per-project or globally.

[Previous](./[1]-Installing-Unity-And-Unity-Hub.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[3]-GameObjects,-Components,-And-Prefabs-In-Unity.md)
