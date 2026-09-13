[Previous](./[5]-Introduction-To-C%2B%2B-In-Unreal-Engine.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[7]-UMG---The-Unreal-Motion-Graphics-UI-Designer.md)

*Building Features*

# Lesson 6 - Unreal Physics And Collision

## 6.1 Collision Presets And Object/Trace Channels

Every Component with a collision shape (Lesson 3.2) has a **Collision Preset** — a named bundle of settings controlling what it collides with, editable in the Details panel:

| Preset | Typical Use |
|---|---|
| `BlockAll` | Solid geometry — walls, floors, most props |
| `OverlapAll` | Trigger volumes — detects entry/exit without physically blocking |
| `Pawn` | Player/AI capsules — blocks world geometry, often overlaps other Pawns |
| `NoCollision` | Purely visual meshes with no physical or trigger presence |

Underneath the presets are two related but distinct concepts:

- **Object Channels** — classify *what a thing is* (`WorldStatic`, `WorldDynamic`, `Pawn`, `Camera`, custom channels like `Interactable`).
- **Trace Channels** — classify *what a line trace/raycast is looking for* (`Visibility`, `Camera`, or custom channels like `Bullet` or `Interact`), independent of an object's own channel.

Each preset defines, per channel, whether to **Ignore**, **Overlap**, or **Block** — this is what determines, for example, whether a bullet trace channel passes through a window mesh while the `WorldDynamic` object channel still lets the same window physically block a thrown Actor. Custom channels are added in **Project Settings > Collision**, useful once a project needs finer-grained rules than the built-in presets provide (e.g. an `Interactable` object channel so a "highlight on look-at" trace only reacts to certain props).

---

## 6.2 Simulating Physics On A Component

Any Component with collision can have **`Simulate Physics`** enabled in its Details panel (or via `Set Simulate Physics` at runtime), handing its movement over to Unreal's physics engine instead of Blueprint/C++ code:

```
StaticMeshComponent (Crate)
├── Simulate Physics: true
├── Mass: 25 kg (auto-computed from mesh, or overridden)
├── Linear Damping / Angular Damping (air resistance-like drag)
└── Enable Gravity: true
```

Once simulating, the Component reacts to gravity, collisions, and any applied forces automatically — you generally stop setting its position/rotation directly, since the physics engine now owns that. Common ways to affect a simulating Component from Blueprints or C++:

- **Add Impulse** — an instant velocity change (a hit, an explosion push).
- **Add Force** — a continuous push applied over time (wind, a thruster).
- **Add Torque** — a rotational force.

A Component must have `Simulate Physics` enabled *and* a valid collision shape for these to have any effect — a common beginner issue is calling `Add Impulse` on a Component that's still set to `NoCollision` or has physics simulation off, which silently does nothing.

---

## 6.3 Overlap Events vs Hit Events

Unreal fires different events depending on whether two colliding shapes are set to **Overlap** or **Block** each other for the relevant channel:

| | Overlap Events | Hit Events |
|---|---|---|
| **Fires when** | Shapes set to Overlap pass through each other | Shapes set to Block physically collide |
| **Key events** | `OnComponentBeginOverlap`, `OnComponentEndOverlap` | `OnComponentHit` |
| **Typical use** | Trigger volumes, pickups, damage zones | Physical impacts — collisions, projectile hits, landing |
| **Requires** | `Generate Overlap Events` enabled on both shapes | Both shapes set to Block each other |

```
[OnComponentBeginOverlap (Event)]
      │ (exec)                    ▲ (data: OtherActor)
      ▼                            │
[Cast To BP_Player] ──▶ [Add Health] ──▶ [Destroy Actor] (the pickup)
```

`OnComponentHit` additionally provides an **Impact Point**, **Impact Normal**, and **Impulse** — useful for spawning a hit-direction particle effect or scaling damage by collision force, information overlap events don't carry since nothing physically collided. A shape can be set to generate both kinds of events for *different* channels simultaneously (e.g. blocking `WorldStatic` while overlapping `Pawn`), which is how a pickup can sit solidly on the floor while still letting the player walk through it to collect it.

---

## 6.4 Physics Constraints

A **Physics Constraint Component** joins two simulating Components together, restricting their relative motion — the basis for doors, ragdolls, rope bridges, and similar jointed setups:

```
PhysicsConstraintComponent
├── Component 1: Door (simulating)
├── Component 2: DoorFrame (static, or World)
├── Angular Swing 1 Limit: 0° to 110° (hinge range)
└── Linear Limits: Locked (no sliding — pure rotation)
```

Common constraint configurations:

- **Hinge** — lock all linear motion, free rotation on one angular axis within a limited range (a door, a lid).
- **Ball-and-socket** — lock linear motion, free rotation on all angular axes (a ragdoll shoulder joint).
- **Slider** — free linear motion on one axis, locked rotation (a piston, a sliding drawer).

Ragdolls extend this pattern across an entire Skeletal Mesh: each bone gets its own simulating physics body, and constraints between adjacent bones (set up in the **Physics Asset**, opened by double-clicking a Skeletal Mesh's associated Physics Asset in the Content Browser) replicate joint limits like an elbow's single-axis bend versus a shoulder's wider range — the animation and physics systems this touches on are covered in more depth in Lesson 8's Skeletal Mesh material.

[Previous](./[5]-Introduction-To-C%2B%2B-In-Unreal-Engine.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[7]-UMG---The-Unreal-Motion-Graphics-UI-Designer.md)