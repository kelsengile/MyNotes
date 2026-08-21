[Previous](./[20]-3D-Physics-and-Collisions.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[22]-Player-Input-and-Controls.md)

*3D Game Development*

# Lesson 21 - Cameras in 3D

## 21.1 Perspective vs Orthographic

- **Perspective camera** — objects appear smaller the further they are from the camera, matching how human vision and real cameras work. This is the default for almost all 3D games.
- **Orthographic camera** — objects appear the same size regardless of distance, useful for isometric games, CAD-style tools, or certain stylized strategy games, even in a 3D-modeled scene.

---

## 21.2 Camera Rigs (First-Person, Third-Person)

- **First-person camera** — placed at (or near) the character's eye level, moving and rotating directly with the player's look input. The player typically never sees their own full body, only hands/weapons.
- **Third-person camera** — positioned behind and above the character, usually attached via a "camera arm" or "spring arm" that maintains a target distance while rotating around the character based on player input.

Third-person camera rigs are more complex than first-person ones because they must also handle obstacles — see below.

---

## 21.3 Field of View and Clipping Planes

- **Field of view (FOV)** — how wide an angle the camera captures. A wider FOV shows more of the scene but can distort edges; a narrower FOV feels more zoomed-in and focused.
- **Near clipping plane** — the closest distance the camera renders; anything closer is invisible (prevents objects from rendering incorrectly when the camera is very close).
- **Far clipping plane** — the furthest distance the camera renders; objects beyond this aren't drawn, which is a common performance optimization.

---

## 21.4 Camera Collision

Without special handling, a third-person camera can end up clipping through walls or other geometry when the player backs into a corner. **Camera collision** solves this by:

1. Casting a ray (or shape) from the character toward the desired camera position.
2. If that ray hits an obstacle, pulling the camera closer to the character instead of letting it pass through the wall.

This is a small detail, but getting it wrong is one of the fastest ways to make an otherwise solid third-person game feel unpolished, since a camera that clips through geometry breaks visibility and immersion immediately.

[Previous](./[20]-3D-Physics-and-Collisions.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[22]-Player-Input-and-Controls.md)
