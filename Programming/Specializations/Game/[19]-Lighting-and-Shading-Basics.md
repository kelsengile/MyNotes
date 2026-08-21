[Previous](./[18]-3D-Models-Meshes-and-Materials.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[20]-3D-Physics-and-Collisions.md)

*3D Game Development*

# Lesson 19 - Lighting & Shading Basics

## 19.1 Types of Lights

- **Directional light** — simulates a distant light source like the sun; all rays travel in parallel, and position doesn't matter, only rotation/direction.
- **Point light** — radiates in all directions from a single point in space, like a light bulb or torch, fading with distance.
- **Spot light** — a cone-shaped light radiating from a point in one direction, like a flashlight.
- **Ambient light** — a base level of light applied evenly across the whole scene, preventing shadowed areas from being pure black.

---

## 19.2 Shading Models

Shading determines how a surface's material responds to light hitting it. Two properties largely define modern shading models:

- **Roughness** — how scattered or focused light reflections appear; a rough surface (concrete) diffuses light broadly, while a smooth surface (polished metal) reflects it sharply.
- **Metallic** — whether a surface behaves like a metal (reflecting the environment's color) or a non-metal/dielectric (reflecting mostly white highlights over its base color).

Most modern engines use **Physically Based Rendering (PBR)**, a shading approach designed to look plausible under any lighting condition by modeling how light behaves in the real world, rather than relying on hand-tuned, scene-specific tricks.

---

## 19.3 Baked vs Real-Time Lighting

- **Real-time lighting** is recalculated every frame, allowing lights to move and change dynamically, but at a higher performance cost.
- **Baked (static) lighting** is precomputed once (often overnight, as part of a build step) and stored as a texture ("lightmap"), which is extremely cheap to render but can't react to changes at runtime.

Many games combine both: static geometry uses baked lighting for performance, while dynamic objects (the player, moving enemies) use real-time lighting so they respond correctly as they move through the scene.

---

## 19.4 Shadows

Shadows dramatically improve a scene's sense of depth and grounding. Key concepts:

- **Shadow casters** — objects that block light and produce a shadow.
- **Shadow receivers** — surfaces the shadow is drawn onto.
- **Shadow resolution/distance** — settings that trade visual quality for performance; higher resolution shadows look sharper but cost more to render, and are often only enabled near the camera to save performance further away.

Getting comfortable adjusting these three areas — light types, shading properties, and shadow settings — covers the large majority of everyday 3D lighting work in a typical small game project.

[Previous](./[18]-3D-Models-Meshes-and-Materials.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[20]-3D-Physics-and-Collisions.md)
