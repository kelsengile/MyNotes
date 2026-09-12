[Previous](./[13]-Scripting-And-Game-Logic.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[15]-Audio-Systems.md)

*Gameplay Systems*

# Lesson 14 - Animation Systems

## 14.1 Keyframe Animation

**Keyframe animation** is the foundational technique behind most game animation: an artist defines the state of an object (its position, rotation, or other properties) at specific points in time, called **keyframes**, and the engine automatically calculates ("interpolates") every frame in between, producing smooth motion from just a handful of defined poses.

For example, animating a door opening might only require two keyframes:

```
Keyframe at 0.0s: door rotation = 0°   (closed)
Keyframe at 0.5s: door rotation = 90°  (open)
```

The engine fills in every rotation value between 0° and 90° across that half-second automatically, based on an **interpolation curve** that controls the pacing of the motion — a linear curve moves at a constant speed, while an "ease-in/ease-out" curve starts and ends slowly with faster motion in the middle, often looking more natural.

| Time | Linear curve | Ease-in/ease-out curve |
|---|---|---|
| 0.0s | 0° | 0° |
| 0.125s | 22.5° | ~8° (starts slow) |
| 0.25s | 45° | 45° (fastest here) |
| 0.375s | 67.5° | ~82° (ends slow) |
| 0.5s | 90° | 90° |

---

## 14.2 Skeletal Animation And Rigging

Simple keyframe animation of a whole object's transform works fine for a door, but animating a character walking, running, or waving requires moving individual body parts independently. This is handled through **skeletal animation**: a 3D character model has an invisible internal "skeleton" (a hierarchy of connected bones, directly using the parent-child transform hierarchy from Lesson 6), and the visible mesh is **rigged** — mathematically bound to that skeleton so that moving a bone deforms the nearby portion of the mesh, similar to how a real skeleton moves skin and muscle.

A simplified skeleton hierarchy for a humanoid character:

```
Root
└── Hips
    ├── Spine
    │   ├── Chest
    │   │   ├── Shoulder_L → Arm_L → Hand_L
    │   │   └── Shoulder_R → Arm_R → Hand_R
    │   └── Head
    ├── Leg_L → Foot_L
    └── Leg_R → Foot_R
```

Animators then keyframe the *rotations of these bones* over time rather than the mesh directly — a walk cycle is really just the leg and arm bones rotating back and forth in a repeating pattern, which the engine applies to deform the character's mesh in real time.

---

## 14.3 Animation Blending And Transitions

Real gameplay rarely involves a single animation playing in isolation — a character might need to smoothly shift from walking to running as speed increases, or blend an upper-body "aiming" animation with lower-body "running" animation at the same time. Engines handle this through **animation blending**: instead of abruptly switching from one animation clip to another, the engine mixes them together over a short period, gradually reducing the weight of the outgoing animation while increasing the weight of the incoming one.

This is what makes character movement feel fluid rather than robotic — a sudden snap from a walk animation directly to a run animation would look jarring, while a blended transition over a few tenths of a second looks natural.

---

## 14.4 Animation State Machines

Just as gameplay logic can be organized using state machines (Lesson 13), animation is typically driven by a dedicated **animation state machine** (often visualized directly in the engine's editor as a graph of connected animation clips). Each state in the graph corresponds to an animation clip (`Idle`, `Walk`, `Run`, `Jump`), and transitions between states are triggered by parameters set from gameplay code — commonly a character's current speed, or whether they're grounded.

A simplified example of how gameplay code might drive an animation state machine:

```
function update(delta_time) {
    animator.set_float("speed", current_movement_speed)
    animator.set_bool("is_grounded", is_touching_ground())
}
```

The animation state machine itself — configured visually by an animator, not hardcoded by a programmer — then decides when to transition from `Idle` to `Walk`, or from `Walk` to `Jump`, based on those parameter values, and handles blending smoothly between them using the technique from Section 14.3. This separation lets programmers and animators work independently: programmers expose meaningful parameters, and animators design how those parameters translate into visual motion.

---

[Previous](./[13]-Scripting-And-Game-Logic.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[15]-Audio-Systems.md)
