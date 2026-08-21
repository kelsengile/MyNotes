[Previous](./[23]-Animation-Systems.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[25]-AI-and-NPC-Behavior-Basics.md)

*Gameplay Systems*

# Lesson 24 - Character Movement & Controllers

## 24.1 Kinematic vs Physics-Based Movement

There are two broad approaches to moving a player character:

- **Kinematic movement** — you directly set the character's position/velocity each frame based on input, ignoring most physics forces. This gives precise, predictable control, common in platformers and action games.
- **Physics-based movement** — you apply forces to a Rigidbody and let the physics engine determine the resulting motion. This can feel more natural for vehicles or physics-driven games, but is harder to make feel precise and responsive.

Most character controllers use a hybrid: kinematic-style control for movement, while still using a collider/rigidbody to interact correctly with the physics world (colliding with walls, standing on moving platforms).

---

## 24.2 Ground Detection

Reliable movement code needs to know whether the character is currently on the ground, which affects whether they can jump, and how gravity/friction should apply. Common techniques:

- **Raycast down** from the character's feet a short distance, checking for a hit (see Lesson 20).
- **Small collider/sphere check** just below the character's feet, testing for overlap with ground colliders.

A common bug source is unreliable ground detection near slopes, stairs, or ledges — dedicating extra care here pays off across the whole game, since nearly every other movement feature depends on it.

---

## 24.3 Jumping and Gravity Tuning

Good jump-feel is one of the most heavily tuned aspects of any platformer or action game. Common techniques to make jumping feel responsive:

- **Coyote time** — allowing the player to jump for a brief window (e.g. 0.1s) after walking off a ledge, forgiving slightly late input.
- **Jump buffering** — remembering a jump press slightly before landing, so a jump input just before touching ground still executes.
- **Variable jump height** — applying stronger downward gravity if the jump button is released early, letting players control jump height with press duration rather than only offering one fixed jump arc.

---

## 24.4 Character Controllers in Engines

Most engines provide a built-in **character controller** component specifically designed for humanoid movement, separate from full rigidbody physics — it handles stepping over small obstacles, moving along slopes, and colliding with the world, while still letting you drive movement directly through code. Understanding when to use a dedicated character controller versus a general-purpose rigidbody is an important early decision, since retrofitting one approach onto the other mid-project is often painful.

[Previous](./[23]-Animation-Systems.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[25]-AI-and-NPC-Behavior-Basics.md)
