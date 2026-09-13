[Previous](./[1]-Installing-Unreal-Engine-And-Epic-Games-Launcher.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[3]-Actors,-Components,-And-Blueprints-In-Unreal.md)

*Getting Started*

# Lesson 2 - The Unreal Editor Interface

## 2.1 The Viewport And Navigation

The **Viewport** is Unreal's central 3D editing canvas — where you place, arrange, and preview your level. Navigation defaults will feel familiar if you've used Godot's or Unity's 3D views:

- **Right-mouse-drag** — look around.
- **W A S D** (while right-click held) — fly through the scene, with mouse-wheel scroll adjusting fly speed while holding right-click.
- **Left-mouse-drag** on an Actor — move it along the ground plane; on-screen gizmos handle move/rotate/scale precisely.
- **F** — focus the camera on the selected Actor.

Unlike Unity's split Scene/Game views, Unreal's **Play In Editor (PIE)** mode runs directly inside the same Viewport by default (via the **Play** button), temporarily taking over as a live, playable preview — though a separate **New Editor Window (PIE)** option is available if you want the running game and the editing viewport visible side by side.

---

## 2.2 The World Outliner

The **World Outliner** lists every **Actor** (Unreal's base entity — covered fully in Lesson 3) currently placed in the open level, similar in role to Unity's Hierarchy or Godot's Scene Dock:

```
World Outliner
├── Floor (StaticMeshActor)
├── DirectionalLight
├── SkyLight
├── PlayerStart
└── BP_Enemy (Blueprint Actor instance)
    └── BP_Enemy_2
```

Actors can be organized into **Folders** within the Outliner (right-click > Create Folder) purely for organizational clarity — this doesn't create parent-child transform relationships the way nesting does in the other engines covered in this course series; Actor-to-Actor **attachment** (making one Actor's transform follow another) is a separate, explicit operation, covered alongside Components in Lesson 3.

---

## 2.3 The Details Panel And Content Browser

Two panels handle very different responsibilities, worth distinguishing early:

- **Details Panel** — shows every property and Component of whichever Actor is currently selected, whether selected in the Viewport or the World Outliner. Unreal's equivalent of the Inspector.
- **Content Browser** — a file browser for your entire project's assets: Blueprints, materials, meshes, sounds, levels themselves — regardless of which level is currently open. Unreal's equivalent of Unity's Project window or Godot's FileSystem Dock.

```
Details Panel (selected: BP_Enemy)       Content Browser (entire project)
┌────────────────────────┐              Content/
│ Transform                 │              ├── Blueprints/
│  Location (0,0,0)          │              │   ├── BP_Enemy
│  Rotation (0,0,0)           │              │   └── BP_Player
│  Scale    (1,1,1)            │              ├── Meshes/
├────────────────────────┤              ├── Materials/
│ Static Mesh Component       │              └── Maps/
│  Mesh: SM_Enemy               │                  └── MainLevel
└────────────────────────┘
```

Double-clicking a Blueprint asset in the Content Browser opens the **Blueprint Editor** (Lesson 4) for that class — the Content Browser is where you'll spend a significant amount of time once you start building reusable Blueprint classes rather than only placing individual Actors.

---

## 2.4 Modes And Toolbars

The **Modes** panel (usually docked near the toolbar) switches the Viewport between different specialized editing tools:

| Mode | Purpose |
|---|---|
| **Select** (default) | Normal Actor selection and transform editing |
| **Landscape** | Sculpting and painting large terrain, Unreal's equivalent of Godot's Terrain Editor or a heightmap-based landscape tool |
| **Foliage** | Painting large numbers of trees/grass/rocks efficiently across a landscape |
| **Mesh Paint** | Painting vertex colors directly onto meshes |

The main toolbar (top of the editor) provides quick access to the most common actions: **Save**, **Play**, **Build** (recompiles lighting and navigation data for the level), and **Platforms** (packaging shortcuts, covered in Lesson 9). Like the other engines in this course series, panel layouts are fully dockable and resizable, and a custom arrangement can be saved via **Window > Save Layout**.

[Previous](./[1]-Installing-Unreal-Engine-And-Epic-Games-Launcher.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[3]-Actors,-Components,-And-Blueprints-In-Unreal.md)