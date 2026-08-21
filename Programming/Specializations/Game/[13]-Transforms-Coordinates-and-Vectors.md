[Previous](./[12]-Component-Based-Architecture.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[14]-Sprites-and-Spritesheets.md)

*Game Engine Fundamentals*

# Lesson 13 - Transforms, Coordinates & Vectors

## 13.1 What Is a Transform?

Every object in a scene has a **transform** — the combination of its:

- **Position** — where it is in space.
- **Rotation** — which way it's facing.
- **Scale** — how big it is, relative to its original size.

The transform is what actually places an object in the world; components like renderers and colliders read the transform to know where to draw or collide.

---

## 13.2 Coordinate Systems

- **2D games** typically use an X (horizontal) and Y (vertical) axis, with the origin `(0, 0)` usually at the center or a corner of the screen/world.
- **3D games** add a Z axis (depth). Different engines orient axes differently — Unity uses Y-up (Y is vertical) with a left-handed coordinate system, while Godot and many other 3D tools use various conventions. Always check your specific engine's documentation rather than assuming.
- **Screen space vs world space**: screen space refers to pixel coordinates on the display; world space refers to positions within the game world itself. UI elements typically use screen space, while gameplay objects use world space.

---

## 13.3 Vectors

A **vector** bundles multiple numbers together to represent a position, direction, or velocity:

- `Vector2(x, y)` for 2D.
- `Vector3(x, y, z)` for 3D.

Common vector operations:

- **Magnitude/length** — how long the vector is (useful for distance checks).
- **Normalization** — scaling a vector to length 1 while keeping its direction, essential when you want a pure direction without magnitude (e.g. movement input).
- **Dot product** — measures how aligned two vectors are; commonly used to check if something is in front of or behind another object.
- **Addition/subtraction** — `targetPosition - currentPosition` gives the direction and distance from one point to another.

```csharp
Vector3 direction = (target.position - transform.position).normalized;
transform.position += direction * speed * deltaTime;
```

---

## 13.4 Local vs World Space

- **World space** coordinates are relative to the entire scene's origin.
- **Local space** coordinates are relative to an object's parent (see Lesson 11's discussion of hierarchies).

For example, a hand-held weapon's local position might be `(0.2, -0.1, 0.5)` relative to the character's hand — regardless of where that character is standing in the world. The engine automatically combines local transforms up through the hierarchy to calculate the final world position, which is what actually gets rendered.

[Previous](./[12]-Component-Based-Architecture.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[14]-Sprites-and-Spritesheets.md)
