[Previous](./[2]-The-Roblox-Studio-Interface.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[4]-Introduction-To-Luau-Scripting.md)

*Building And Scripting*

# Lesson 3 - The Roblox Instance Hierarchy

## 3.1 Instances As Roblox's Building Block

Just as everything in Godot is a **node**, everything in Roblox is an **Instance**. A Part, a Script, a Sound, a Light, a folder used purely for organization — all Instances, all inheriting from a common base class that gives them a `Name`, a `Parent`, and the ability to hold `Children` of their own.

Every Instance has:

- A **ClassName** (`Part`, `Script`, `Sound`, `PointLight`, etc.) determining what it can do.
- A **Name**, shown in the Explorer and used to find it in code.
- A **Parent**, which places it somewhere in the hierarchy — set to `nil` to remove it from the world entirely.
- Class-specific **properties**, editable in the Properties panel (a Part's `Size`, a Script's `Source`, etc.).

Because everything shares this common Instance foundation, the same core operations — parenting, cloning, destroying, finding children — work identically whether you're dealing with a Part or a Sound or a ScreenGui, which is a deliberate design choice that keeps the API consistent everywhere.

---

## 3.2 Key Services (Workspace, Players, ReplicatedStorage, ServerScriptService)

At the top level of every place's Explorer sit fixed **Services** — singleton Instances Roblox creates automatically, each responsible for one area of functionality:

| Service | Purpose |
|---|---|
| **Workspace** | Holds everything physically present in the 3D world — Parts, Models, terrain. If it's visible and has a physical location, it lives here. |
| **Players** | Manages connected player objects; each connected player gets a `Player` Instance here automatically while in the experience. |
| **ReplicatedStorage** | Holds Instances (and ModuleScripts) that both the server *and* every client can see — the standard place to put shared assets, RemoteEvents, and shared code. |
| **ServerScriptService** | Holds Scripts meant to run **only** on the server — never replicated to clients, making it the correct place for anything security-sensitive (covered fully in Lesson 5.4). |
| **StarterGui / StarterPack / StarterPlayer** | Templates copied into each player's client when they join — used to give every new player a starting UI, tools, or character setup. |
| **Lighting** | Controls global lighting, sky, atmosphere, and time-of-day settings for the whole place. |

Understanding *which service something lives in* tells you a lot about its purpose and visibility before even opening it — a Script under `ServerScriptService` is server-only by construction, while anything under `ReplicatedStorage` is deliberately meant to be seen by everyone.

---

## 3.3 Parent-Child Relationships In The Explorer

Like a scene tree, the Explorer's hierarchy is a strict parent-child tree, and parenting has real functional consequences — not just visual organization:

```
Workspace
└── SwordModel (Model)
    ├── Blade (Part)
    ├── Handle (Part)
    └── SwordScript (Script)
```

- Moving `SwordModel` moves `Blade` and `Handle` along with it, the same way moving a parent node moves its children in Godot.
- A `Script` parented under a Part or Model only runs in relation to that context — it's common to parent gameplay logic directly under the object it controls, so `:GetChildren()` or `.Parent` immediately gives you a reference to what the script is meant to affect.
- **Models** are a special grouping Instance (like an empty organizational node) used specifically to group related Parts together — a `Model` itself has no physical presence, but defines a `PrimaryPart` used as a reference point for moving/orienting the whole group as one unit.

Changing an Instance's `Parent` property in code is the standard way to add or remove something from the world at runtime — setting `Parent = Workspace` makes something appear, and `Parent = nil` (or calling `:Destroy()`) removes it.

---

## 3.4 Finding Instances In Code

Scripts locate Instances relative to wherever the script itself is running from, using **paths** through the hierarchy — conceptually similar to `$NodePath` in Godot, but written as Luau expressions.

```lua
-- Dot notation: works when the child's name is a known, fixed identifier
local workspace_part = workspace.Baseplate

-- FindFirstChild: safer when the name is dynamic or might not exist
local sword = workspace:FindFirstChild("SwordModel")

-- WaitForChild: pauses the script until the Instance exists
-- essential when something might not have replicated/loaded yet
local sword = workspace:WaitForChild("SwordModel")
```

The difference between these three matters in practice:

| Method | Behavior If Missing |
|---|---|
| `workspace.SwordModel` | Throws an error immediately |
| `workspace:FindFirstChild("SwordModel")` | Returns `nil` — you must check before using it |
| `workspace:WaitForChild("SwordModel")` | Yields (pauses) the script until it appears, or times out with a warning |

`WaitForChild` is especially important for **LocalScripts** (Lesson 5.2), since client-side code often runs before every server-replicated Instance has finished arriving — using dot notation there can cause intermittent errors that only show up under network latency, making `WaitForChild` the safer default whenever you're not certain something already exists.

[Previous](./[2]-The-Roblox-Studio-Interface.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[4]-Introduction-To-Luau-Scripting.md)
