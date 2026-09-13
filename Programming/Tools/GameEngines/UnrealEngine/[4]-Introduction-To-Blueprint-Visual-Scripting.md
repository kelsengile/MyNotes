[Previous](./[3]-Actors,-Components,-And-Blueprints-In-Unreal.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[5]-Introduction-To-C%2B%2B-In-Unreal-Engine.md)

*Core Concepts And Scripting*

# Lesson 4 - Introduction To Blueprint Visual Scripting

## 4.1 The Blueprint Editor Layout

Double-clicking a Blueprint asset in the Content Browser opens the **Blueprint Editor**, a dedicated workspace distinct from the main level-editing Viewport, laid out around a few key panels:

```
┌───────────┬───────────────────────┬───────────┐
│ Components  │                          │  Details    │
│  panel       │      Event Graph          │  panel       │
│ (this class's │   (node-based scripting)   │ (selected     │
│  Components)  │                            │  node/var)    │
├───────────┴───────────────────────┴───────────┤
│  My Blueprint (Variables, Functions, Event dispatchers)    │
└──────────────────────────────────────────────────────┘
```

- **Components** — the same Components panel from Lesson 3.2, showing/editing this class's attached Components.
- **Event Graph** — the main canvas where you build logic by connecting nodes (covered next).
- **My Blueprint** — lists this class's own Variables, Functions, and Event Dispatchers (Blueprint's version of signals/events), similar in spirit to a script's declared fields and methods in the text-based engines.
- **Details** — shows properties of whatever node or variable is currently selected.

A Blueprint Editor tab exists per-class — opening `BP_Enemy` and `BP_Player` gives you two separate tabs, each with its own Event Graph, the same way each `.gd` or `.cs` script file is its own separate document in the other engines.

---

## 4.2 Nodes, Pins, And Execution Flow

Blueprint logic is built from **nodes** connected by **pins**, and understanding the two different kinds of connections is the single most important concept for reading or writing a Blueprint graph:

- **Execution pins** (white, arrow-shaped) — control *when* something happens, flowing left to right like a flowchart. A node only runs once execution flow reaches it.
- **Data pins** (colored by type — blue for Boolean, green for Float, pink for String/Text, etc.) — carry *values* between nodes, and don't determine timing on their own.

```
[Event BeginPlay]                         [Get Player Pawn]
      │ (exec)                                    │ (data: Pawn)
      ▼                                            ▼
[Branch] ◀── condition (bool, data pin) ── [Is Valid?]
   │ True
   ▼
[Print String] ◀── "Player found!" (data: string)
```

A node without an execution pin at all (like `Get Player Pawn` above) is a **pure** node — it has no side effects and simply computes/returns a value whenever something asks for it, rather than needing to be "run" in sequence. Most getter-style nodes (getting a variable, doing math, checking a condition) are pure; most action-style nodes (spawning something, printing to screen, moving an Actor) require an execution pin connection to actually run.

---

## 4.3 Variables And Functions In Blueprints

**Variables** are declared in the My Blueprint panel, given a name and a type, and can optionally be marked **Editable** (the Blueprint equivalent of a public/`@export` field — it becomes editable per-instance in the Details panel when this Blueprint is placed in a level or used as a component).

```
My Blueprint > Variables
├── Health          (Float, Editable)
├── MaxHealth        (Float, Editable)
└── IsAlive           (Boolean)
```

**Functions** are separate, reusable, callable graphs — created via the `+` next to Functions in My Blueprint — that can take input parameters and return output values, called from the main Event Graph or from other Blueprints:

```
Function: TakeDamage(Amount: Float)
[Function Entry] ──▶ [Health = Health - Amount] ──▶ [Branch: Health <= 0]
                                                          │ True
                                                          ▼
                                                    [Destroy Actor]
```

This mirrors declaring a function in GDScript or C# almost exactly — the difference is purely that the function body is built from connected nodes rather than typed statements, while the underlying concept (reusable, named, parameterized logic) is identical.

---

## 4.4 Event Graphs vs Function Graphs

It's worth clearly distinguishing these two graph types, since new Blueprint users sometimes mix them up:

| | Event Graph | Function Graph |
|---|---|---|
| **Starts with** | A built-in Event node (`BeginPlay`, `Tick`, `OnComponentHit`, a custom Event) | A `Function Entry` node with defined parameters |
| **When it runs** | Automatically, whenever that Event fires | Only when explicitly called — from the Event Graph, another Function, or another Blueprint |
| **Purpose** | React to things happening (spawn, frame update, collision) | Package reusable logic into a named, callable unit |

A single Blueprint class typically has **one** Event Graph but **many** Functions — the Event Graph reacts to what happens to this Actor, and calls out to Functions to keep that reaction logic organized and reusable, rather than building one enormous, unreadable graph directly inside `BeginPlay`/`Tick`. As a Blueprint grows, moving self-contained pieces of logic out of the Event Graph and into named Functions is the standard way to keep it maintainable — directly analogous to breaking a long script into smaller named methods in any text-based language.

[Previous](./[3]-Actors,-Components,-And-Blueprints-In-Unreal.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[5]-Introduction-To-C%2B%2B-In-Unreal-Engine.md)