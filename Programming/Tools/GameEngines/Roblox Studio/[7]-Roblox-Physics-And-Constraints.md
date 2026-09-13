[Previous](./[6]-Building-Parts,-Models,-And-Terrain.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[8]-GUI-With-Roblox-UI-Elements.md)

*Advanced Experience Design*

# Lesson 7 - Roblox Physics And Constraints

## 7.1 Anchored vs Unanchored Parts

Every Part has an `Anchored` property, and it's the single most important physics switch in Roblox:

- **Anchored = true** — the Part is fixed in place. Gravity and collisions from other objects don't move it, though it can still be moved by code (`Position`, `CFrame`) or by animation. Used for static level geometry: floors, walls, buildings.
- **Anchored = false** — the Part is fully simulated by the physics engine: affected by gravity, falls if unsupported, and responds to collisions realistically. Used for anything meant to move naturally: crates, vehicles, ragdolled characters.

```
Anchored = true          Anchored = false
┌──────────┐              ┌──────────┐
│  Floor   │  (fixed)      │  Crate   │  (falls, tips over,
└──────────┘               └──────────┘   responds to pushes)
```

A common beginner mistake is leaving structural geometry unanchored — an entire unanchored building will simply collapse under gravity the moment the game starts, since nothing is holding its Parts together unless a `WeldConstraint` (7.2) or anchoring keeps them in place.

---

## 7.2 Constraints (Hinge, Spring, Weld, Motor)

**Constraints** are special Instances that define a physical relationship between two Parts, letting you build doors, vehicles, and mechanisms without scripting the movement by hand. Common constraint types:

| Constraint | Behavior |
|---|---|
| **WeldConstraint** | Rigidly locks two Parts together as if they were one — the standard way to attach unanchored Parts into a single solid structure. |
| **HingeConstraint** | Allows rotation around a single axis, like a door hinge or a wheel axle. Can be motorized to spin automatically. |
| **SpringConstraint** | Simulates a spring between two Parts — stretches and compresses with configurable stiffness/damping. |
| **Motor6D** / **MotorConstraint** | Drives rotation directly, commonly used for character joints and animated mechanisms. |

A simple swinging door, for example:

```
DoorFrame (Anchored Part)
└── Door (Unanchored Part)
    └── HingeConstraint (Attachment0: DoorFrame, Attachment1: Door)
```

Constraints connect via **Attachment** Instances — small invisible points placed on each Part marking exactly where the constraint's connection is anchored — giving precise control over the pivot point or connection location rather than just "somewhere on the Part."

---

## 7.3 Collision Groups

By default, every Part can collide with every other Part. **Collision Groups** (configured via the Model tab's Collision Groups editor, or in code with `PhysicsService`) let you define named groups of Parts and control which groups can pass through each other — conceptually similar to Godot's collision layers/masks from the earlier Topic.

```lua
local PhysicsService = game:GetService("PhysicsService")

PhysicsService:RegisterCollisionGroup("Projectiles")
PhysicsService:RegisterCollisionGroup("Players")

-- Projectiles should not collide with the player who fired them
PhysicsService:CollisionGroupSetCollidable("Projectiles", "Players", false)
```

Once registered, a Part is assigned to a group via its `CollisionGroup` property. This is the standard tool for problems like "let players pass through each other but not through walls," or "bullets shouldn't collide with the player who fired them" — avoiding awkward workarounds like disabling `CanCollide` entirely.

---

## 7.4 CFrame And Vector3 Basics

Two data types underlie virtually all positioning and movement code in Roblox:

- **`Vector3`** — a point or direction in 3D space: `Vector3.new(x, y, z)`. Used for `Position`, `Size`, and directional math (e.g. velocity).
- **`CFrame`** (Coordinate Frame) — represents both a **position and an orientation** together, unlike `Vector3` which only holds position. Most Instances actually use `CFrame` internally (`Position` is really a convenience shortcut into a Part's `CFrame`).

```lua
local part = workspace.Baseplate

-- Vector3: just a position
part.Position = Vector3.new(0, 10, 0)

-- CFrame: position AND rotation together
part.CFrame = CFrame.new(0, 10, 0) * CFrame.Angles(0, math.rad(45), 0)

-- Moving relative to a Part's own current orientation ("move forward")
part.CFrame = part.CFrame * CFrame.new(0, 0, -5)
```

That last example is a pattern worth understanding well: multiplying a `CFrame` by a relative offset moves *along that CFrame's own local axes*, rather than world axes — this is how you correctly move something "forward relative to where it's facing," a very common need for vehicles, NPCs, and camera code.

`CFrame:ToWorldSpace()` and `:ToObjectSpace()` (and the `*` operator shown above) handle the underlying matrix math for you — you rarely need to work with raw rotation matrices directly in day-to-day Roblox scripting.

[Previous](./[6]-Building-Parts,-Models,-And-Terrain.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[8]-GUI-With-Roblox-UI-Elements.md)
