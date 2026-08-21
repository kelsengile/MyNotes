[Previous](./[22]-Player-Input-and-Controls.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[24]-Character-Movement-and-Controllers.md)

*Gameplay Systems*

# Lesson 23 - Animation Systems

## 23.1 Keyframe Animation

**Keyframe animation** defines an object's state (position, rotation, a sprite frame, etc.) at specific points in time, called keyframes, and lets the engine automatically **interpolate** (smoothly blend) the values in between. A simple bounce animation, for example, might only need three keyframes: ground, peak, ground — the engine fills in the smooth motion between them.

---

## 23.2 Animation State Machines

Characters typically have many possible animations (idle, walk, run, jump, attack), and an **animation state machine** manages which one is currently playing and how they transition:

- Each **state** represents one animation (e.g. "Walking").
- **Transitions** define the rules for moving between states (e.g. "Idle → Walking when speed > 0").
- Transitions are driven by **parameters** your gameplay code sets each frame, such as a `speed` float or an `isGrounded` bool.

This keeps animation logic organized and visual (often edited in a graph-based editor) rather than a tangle of manual `if` statements scattered through gameplay code.

---

## 23.3 Blending and Transitions

Snapping instantly between animations looks jarring. **Blending** smoothly crossfades from one animation to another over a short duration, so, for example, the last frame of a walk cycle gradually merges into the first frame of a run cycle rather than popping abruptly. Some systems also support **blend trees**, which mix multiple animations based on a continuous parameter — for example, smoothly blending between "walk" and "run" animations as speed increases, rather than only having two discrete states.

---

## 23.4 Rigging and Skeletal Animation (Overview)

For characters (especially 3D ones), animation is usually driven by a **skeleton** — a hierarchy of connected "bones" that deform the mesh when moved, much like a real skeleton moving skin and muscle:

- **Rigging** is the process (usually done in external 3D software) of building this bone hierarchy and binding it to a mesh's vertices.
- **Skeletal animation** then works by animating the bones' rotations over time; the mesh automatically deforms to follow.
- **Inverse kinematics (IK)** is a technique that calculates bone rotations backward from a target (e.g. "keep this foot planted on uneven ground") rather than only ever playing pre-authored animation clips directly.

Most of this rigging work happens outside the game engine, but understanding the pipeline helps when importing and troubleshooting animated 3D models (see also Lesson 18 and Lesson 39).

[Previous](./[22]-Player-Input-and-Controls.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[24]-Character-Movement-and-Controllers.md)
