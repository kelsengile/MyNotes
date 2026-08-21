[Previous](./[16]-2D-Physics-and-Collisions.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[18]-3D-Models-Meshes-and-Materials.md)

*2D Game Development*

# Lesson 17 - Cameras in 2D

## 17.1 The 2D Camera

A camera defines what part of the world is actually visible on screen. In 2D, cameras are almost always **orthographic** — objects appear the same size regardless of their distance from the camera, unlike the perspective cameras common in 3D (Lesson 21). Key camera settings include:

- **Position** — where in the world the camera is centered.
- **Orthographic size / zoom** — how much of the world is visible at once.
- **Background color** — what fills any area not covered by scene content.

---

## 17.2 Camera Following

Most 2D games move the camera to follow the player rather than keeping it fixed. A simple follow script tracks the target's position, often with smoothing so the camera doesn't feel robotic:

```csharp
void LateUpdate() {
    Vector3 targetPosition = player.position + offset;
    transform.position = Vector3.Lerp(transform.position, targetPosition, smoothSpeed * deltaTime);
}
```

Using `LateUpdate` (which runs after all `Update` calls each frame) ensures the camera reacts to the player's *final* position for that frame, avoiding jitter.

---

## 17.3 Camera Bounds

Without limits, a following camera can show areas outside the intended level (empty space, unfinished background). **Camera bounds** clamp the camera's position so it never shows beyond the level's edges — commonly implemented by clamping the camera's X/Y position to a min/max range calculated from the level size and camera's visible area.

---

## 17.4 Zoom and Parallax

- **Zoom** changes the orthographic size (or field of view in 3D) to show more or less of the world — used for dramatic effect or to give players more visibility during fast-paced moments.
- **Parallax scrolling** moves background layers at a different speed than the foreground, creating an illusion of depth in a 2D scene. Layers further from the "camera" (distant mountains, sky) move slower than nearer layers (midground trees), even though everything is technically flat.

Combining smooth following, sensible bounds, and subtle parallax is often what separates a 2D game that feels professionally polished from one that feels rough, even when the core gameplay is identical.

[Previous](./[16]-2D-Physics-and-Collisions.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[18]-3D-Models-Meshes-and-Materials.md)
