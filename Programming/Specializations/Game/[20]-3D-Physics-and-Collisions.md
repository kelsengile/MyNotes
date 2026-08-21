[Previous](./[19]-Lighting-and-Shading-Basics.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[21]-Cameras-in-3D.md)

*3D Game Development*

# Lesson 20 - 3D Physics & Collisions

## 20.1 Rigidbodies in 3D

A **Rigidbody** component makes an object subject to physics simulation — gravity, forces, and collisions with other physics objects. Key properties:

- **Mass** — affects how much force is needed to move the object, and how it behaves in collisions with other bodies.
- **Drag** — resistance that slows movement over time, simulating air resistance.
- **Kinematic flag** — when enabled, the object is moved directly through code rather than physics forces, similar to the 2D concept covered in Lesson 16.

---

## 20.2 Colliders in 3D

Just as in 2D, 3D objects need a collider shape separate from their visual mesh:

- **Box collider** — a cuboid, cheap and common for crates, walls, simple props.
- **Sphere collider** — a sphere, very cheap to compute, good for balls or rough approximations.
- **Capsule collider** — a cylinder with rounded ends, the standard shape for humanoid characters since it slides smoothly over uneven terrain.
- **Mesh collider** — matches a model's exact geometry; the most accurate but also the most expensive, generally reserved for static level geometry rather than moving objects.

---

## 20.3 Forces and Gravity

Physics objects respond to forces applied over time:

```csharp
rigidbody.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
```

- **Continuous forces** (like wind) are applied every physics tick.
- **Impulse forces** (like a jump or explosion) are applied instantly, giving an immediate change in velocity.
- **Gravity** is itself just a constant downward force, typically applied automatically by the engine unless disabled per-object.

---

## 20.4 Raycasting

A **raycast** shoots an invisible line from a point in a direction and reports what it hits — one of the most versatile tools in 3D game development:

```csharp
if (Physics.Raycast(transform.position, transform.forward, out RaycastHit hit, maxDistance)) {
    Debug.Log("Hit: " + hit.collider.name);
}
```

Common raycasting uses:

- Checking if a character is standing on the ground (a short ray cast downward).
- Implementing hitscan weapons (an instantaneous "shot" rather than a simulated bullet).
- Detecting what object the player is looking at or clicking on.
- Line-of-sight checks for AI (Lesson 25).

[Previous](./[19]-Lighting-and-Shading-Basics.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[21]-Cameras-in-3D.md)
