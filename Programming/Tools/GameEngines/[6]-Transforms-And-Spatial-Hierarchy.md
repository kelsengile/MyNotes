[Previous](./[5]-GameObjects-And-Components.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[7]-Introduction-To-Rendering.md)

*World And Object Management*

# Lesson 6 - Transforms And Spatial Hierarchy

## 6.1 Position, Rotation, and Scale

Every object placed in a scene has a **transform** — the data that describes where it is, how it's oriented, and how big it is. A transform is almost always made up of three parts:

- **Position** — where the object sits in space, typically stored as coordinates (x, y in 2D; x, y, z in 3D).
- **Rotation** — how the object is oriented, often stored as an angle (2D) or as Euler angles/quaternions (3D).
- **Scale** — how large the object is relative to its original size, usually stored per axis (e.g., scale x2 on the x-axis only would stretch an object horizontally).

Together, these three values define exactly how an object's shape and appearance map into the world. Nearly every other system in an engine — rendering, physics, audio — reads an object's transform to know where to draw it, simulate it, or play sound from it.

**Example transform for a 2D enemy sprite:**

| Property | Value | Meaning |
|---|---|---|
| Position | (120, 45) | 120 units right, 45 units up from the origin |
| Rotation | 15° | Tilted slightly clockwise |
| Scale | (2.0, 2.0) | Twice the sprite's original width and height |

---

## 6.2 Parent-Child Hierarchies

As introduced in Lesson 4's scene graph, objects can be nested inside one another, forming a **parent-child hierarchy**. When an object is a "child" of another "parent" object, the child's transform is interpreted *relative to* its parent, and moving the parent automatically moves all of its children along with it.

This is extremely useful for modeling real-world relationships. Consider a car:

```
Car (parent)
├── Wheel_FrontLeft (child)
├── Wheel_FrontRight (child)
├── Wheel_RearLeft (child)
├── Wheel_RearRight (child)
└── Driver (child)
```

If the `Car`'s position changes, every wheel and the driver move with it automatically — you don't need to manually reposition each wheel every frame. Similarly, rotating the car rotates all of its children around it, which is exactly how you'd expect a car and its parts to behave.

---

## 6.3 Local Space vs World Space

Because of parent-child hierarchies, a transform's position, rotation, and scale can be described in two different ways:

- **Local space** — the transform values relative to the object's *parent*. A wheel's local position might be "0.8 units to the right, 0.2 units down" relative to the car's origin, regardless of where the car itself is in the world.
- **World space** — the transform values relative to the entire scene's origin (0, 0, 0). This is the "true" position an object actually occupies, taking every ancestor's transform into account.

If the car above is parked at world position (10, 0) and the front-left wheel's local position is (0.8, -0.2) relative to the car, then the wheel's world position is (10.8, -0.2) — the parent's world position combined with the child's local offset. Engines calculate this automatically, but understanding the difference matters whenever you're debugging "why is this object in the wrong place," since a script that reads local position when it should read world position (or vice versa) is one of the most common sources of positioning bugs.

---

## 6.4 Transform Matrices

Under the hood, position, rotation, and scale are combined into a single mathematical structure called a **transformation matrix**. A matrix lets an engine represent all three operations — translating (moving), rotating, and scaling — as one combined operation that can be applied efficiently to any point on an object.

You rarely need to write matrix math by hand when using an engine's editor or high-level scripting API — you'll usually just set a position, rotation, and scale on a component, and the engine builds the matrix for you. However, understanding that a "transform" is ultimately just a matrix becomes important once you touch shaders (Lesson 8) or write custom rendering code, since the GPU works directly with these matrices to determine exactly where each vertex of a 3D model ends up on screen.

A simplified 2D transformation matrix combining translation (tx, ty), rotation (θ), and scale (sx, sy) looks like this:

```
| sx·cos(θ)   -sy·sin(θ)   tx |
| sx·sin(θ)    sy·cos(θ)   ty |
| 0            0            1 |
```

Multiplying a point's coordinates by this matrix applies the scale, then the rotation, then the translation, all in a single step — which is exactly what happens (in 3D, with a larger matrix) every time an engine renders a single frame of your game.

---

[Previous](./[5]-GameObjects-And-Components.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[7]-Introduction-To-Rendering.md)
