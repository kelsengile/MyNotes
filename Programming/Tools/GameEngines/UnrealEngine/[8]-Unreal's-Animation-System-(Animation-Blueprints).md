[Previous](./[7]-UMG---The-Unreal-Motion-Graphics-UI-Designer.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[9]-Packaging-And-Deploying-An-Unreal-Project.md)

*Building Features*

# Lesson 8 - Unreal's Animation System (Animation Blueprints)

## 8.1 Skeletal Meshes And Skeletons

A **Skeletal Mesh** is an animated 3D mesh whose vertices are deformed by an underlying bone hierarchy — the **Skeleton** asset — rather than staying rigid like a Static Mesh (Lesson 3.2):

```
Skeleton (UE4_Mannequin_Skeleton)
├── pelvis
│   ├── spine_01
│   │   └── spine_02 ─▶ ... ─▶ head
│   ├── thigh_l ─▶ calf_l ─▶ foot_l
│   └── thigh_r ─▶ calf_r ─▶ foot_r
```

Each vertex in the mesh is **weighted** to one or more bones (a process called skinning, typically done in an external DCC tool like Blender or Maya before import), so moving a bone deforms the nearby mesh region proportionally to its weight — the character's arm mesh follows the `upperarm`/`lowerarm` bones bending, for example. Multiple Skeletal Meshes can share the same Skeleton asset (a base character body and separate attachable clothing meshes, for instance), which is what allows a single set of animations to drive any of them interchangeably, since animations are authored against the Skeleton's bone hierarchy, not a specific mesh.

---

## 8.2 Animation Sequences And Montages

An **Animation Sequence** is a single imported animation clip — a walk cycle, a jump, an attack swing — stored as keyframed bone transforms over time against a specific Skeleton.

A **Montage** wraps one or more Animation Sequences with additional playback control, and is the standard tool for anything more complex than a simple looping clip:

```
AM_Attack_Combo (Montage)
├── Section: "Swing1"  (Sequence: Attack_01, 0.0s–0.6s)
├── Section: "Swing2"  (Sequence: Attack_02, 0.6s–1.1s)
└── Notify: "DealDamage" @ 0.4s into Swing1
```

- **Sections** allow branching or looping within a single Montage (combo attacks that can chain into the next swing, or blend back to a different one on a missed input window).
- **Notifies** are markers fired at a specific point in playback, hooked up as Blueprint events — the standard way to sync gameplay logic (spawning a hit trace, playing a footstep sound) to an exact animation frame rather than guessing timing with a Timer.

Montages are played via `Play Montage` (Blueprint node or C++ equivalent) directly on a Skeletal Mesh Component, and — importantly — automatically blend with whatever the Animation Blueprint below is already producing, so a one-off attack swing can play over a looping locomotion animation without manually managing the transition.

---

## 8.3 Animation Blueprints And State Machines

An **Animation Blueprint** (Anim BP) is a specialized Blueprint class assigned to a Skeletal Mesh Component that determines its final pose every frame, built from two connected graphs:

```
Event Graph                          AnimGraph
[Event Blueprint Update Animation]   [State Machine] ──▶ [Output Pose]
      │
      ▼
[Get Velocity] ──▶ [Set Speed variable]
```

- **Event Graph** — runs every frame like an Actor's, typically used to read gameplay data (velocity, whether the character is falling) into variables the AnimGraph can use.
- **AnimGraph** — a separate pure-data graph (no execution pins) that computes the actual bone pose to display, most commonly built around a **State Machine**.

A State Machine organizes animations into distinct **states** connected by **transitions**, evaluated every frame:

```
[Idle] ──(Speed > 10)──▶ [Walk/Run] ──(Speed > 10)──▶ ...
   ▲                          │
   └────(Speed <= 10)─────────┘
              │
      (IsFalling == true)
              ▼
          [Falling/Jump]
```

Each state holds an animation (often itself a **Blend Space**, covered next) and each transition has a condition — a boolean expression referencing the Event Graph's variables — checked continuously, so the character automatically moves from `Idle` to `Walk/Run` the instant `Speed` crosses the threshold, without any explicit "play this animation now" call from gameplay code.

---

## 8.4 Blend Spaces

A **Blend Space** smoothly interpolates between multiple Animation Sequences based on one or two float inputs, rather than hard-cutting between discrete clips — the standard solution for locomotion, where a character needs every speed and direction in between "idle" and "sprint" to look continuous:

```
Blend Space: BS_Locomotion
              Speed (Y axis)
                600 ┤  Sprint_F
                300 ┤  Jog_F
                  0 ┤  Idle ─────── (X axis: Direction, -180° to 180°)
                    └──────────────
```

At runtime, the current `Speed` and `Direction` values (fed in from the Event Graph, exactly like the plain `Speed` variable used in the State Machine example above) act as a 2D coordinate into this grid, and the Blend Space blends the nearest surrounding Animation Sequences proportionally — a character moving at 150 speed sits partway between `Idle` and `Jog`, producing a smooth in-between pose rather than popping from one clip to the other. A 1D Blend Space (a single axis, most commonly just `Speed`) covers simpler cases like a non-strafing walk-to-run blend, while the 2D version above is the standard setup for 8-directional strafing locomotion seen in most third-person character controllers.

[Previous](./[7]-UMG---The-Unreal-Motion-Graphics-UI-Designer.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[9]-Packaging-And-Deploying-An-Unreal-Project.md)