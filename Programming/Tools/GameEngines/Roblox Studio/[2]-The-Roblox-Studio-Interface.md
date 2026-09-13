[Previous](./[1]-Getting-Started-With-Roblox-Studio.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[3]-The-Roblox-Instance-Hierarchy.md)

*Getting Started*

# Lesson 2 - The Roblox Studio Interface

## 2.1 The Explorer And Properties Panels

Two panels anchor almost everything you do in Studio, similar in spirit to a scene tree and inspector in other engines:

- **Explorer** — shows the full hierarchy of every **Instance** in your place, nested under **Services** like `Workspace`, `Players`, and `Lighting`. This is Roblox's equivalent of a scene tree, though the top-level nodes are fixed services rather than a single freely-named root.
- **Properties** — shows every editable property of whichever Instance is currently selected in the Explorer: a Part's size and color, a Script's `Enabled` state, a Sound's volume, and so on.

```
Explorer
├── Workspace
│   ├── Baseplate (Part)
│   └── SpawnLocation
├── Players
├── Lighting
├── ReplicatedStorage
├── ServerScriptService
└── StarterGui
```

Selecting `Baseplate` in the Explorer shows its properties — `Size`, `Position`, `Color`, `Anchored`, and more — in the Properties panel, editable directly or by dragging handles in the viewport.

---

## 2.2 The 3D Viewport And Navigation

The central **Viewport** is where you visually place and arrange your world. Navigation defaults are similar to many 3D tools:

- **Right-mouse-drag** — look around (camera rotation).
- **W A S D** (while right-click is held) — fly through the scene.
- **Scroll wheel** — zoom in/out.
- **F** (with something selected) — focus the camera on the selected Instance.

The toolbar above the viewport provides the core building tools: **Select**, **Move**, **Scale**, **Rotate**, and **Transform** — each with on-screen handles that appear on the selected Part, letting you manipulate it directly rather than only through the Properties panel.

A **grid snapping** option (visible in the Model tab) controls whether moving/rotating a Part snaps to fixed increments — useful for keeping structures aligned, and easy to toggle off when you want free placement.

---

## 2.3 The Toolbox

The **Toolbox** (opened from the View tab) gives you access to a massive library of community- and Roblox-made assets: models, meshes, decals, audio, and plugins, searchable by keyword.

```
┌─────────────────────────────┐
│  Toolbox                     │
├─────────────────────────────┤
│  🔍 [ search... ]             │
│  Models | Meshes | Audio |    │
│  Decals | Plugins             │
├─────────────────────────────┤
│  [img] [img] [img]            │
│  Tree   Rock   Car             │
└─────────────────────────────┘
```

Dragging an asset from the Toolbox into the Viewport inserts it directly into your place. This is one of Roblox's biggest advantages for beginners — you can populate a world with reasonable-looking assets immediately, without needing to model or texture anything yourself, and inspect how community-made models are built to learn from them.

> **Caution:** not everything in the Toolbox is free of scripts you should trust blindly — when importing community models, it's good practice to check what scripts (if any) come attached before using them in a real project, especially anything requesting unusual permissions.

---

## 2.4 Test Mode (Play, Run, Server/Client)

Studio's Play controls (top-center toolbar) offer several distinct testing modes, and picking the right one matters because Roblox experiences run across a **client-server model** (introduced properly in Lesson 5):

| Mode | What It Simulates |
|---|---|
| **Play** | A full client — spawns your character and lets you play as a normal player would, with an in-editor simulated server running behind it. |
| **Run** | Starts the simulated server only, without spawning a player character — useful for testing server-side logic in isolation. |
| **Play Here** | Like Play, but spawns you at your current camera position rather than the default spawn point. |
| **Server/Client multi-test** | Launches multiple simulated clients alongside the server at once, for testing multiplayer interactions (e.g. two players seeing each other) without needing to publish first. |

While in any test mode, the Explorer temporarily shows two parallel views — one for the server's state, one for each client's — which becomes important once you're debugging code that behaves differently depending on which side it runs on (Lesson 5.2). Clicking the **Stop** button (or pressing the shortcut again) ends the simulation and returns you to normal editing.

[Previous](./[1]-Getting-Started-With-Roblox-Studio.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[3]-The-Roblox-Instance-Hierarchy.md)
