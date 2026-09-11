[Previous](./[8]-Materials-Shaders-And-Lighting.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[10]-Physics-Engines.md)

*Rendering*

# Lesson 9 - 2D Vs 3D Rendering

## 9.1 Sprites And 2D Rendering

In 2D games, the fundamental visual unit is the **sprite** — a flat 2D image (often with transparency) drawn at a position in the world. Sprites are usually drawn as simple textured rectangles ("quads"), and animation is achieved by rapidly swapping between a sequence of sprite images (a **sprite sheet**), similar to a flip-book.

2D rendering relies heavily on **layering** and **sorting order** to determine which sprites appear in front of others — since there's no true depth in a flat 2D scene, engines typically let developers assign an explicit sort order (sometimes called a "z-order" or "sorting layer") to control draw order, such as making sure the player always renders in front of background scenery but behind foreground decoration.

## 9.2 Meshes And 3D Rendering

In 3D games, objects are represented by **meshes** — collections of vertices connected into triangles that define a 3D shape's surface. A simple mesh might be a cube made of just 8 vertices and 12 triangles; a detailed character model might use tens of thousands of triangles to capture fine detail.

Unlike 2D sprites, 3D meshes have real depth, so the renderer needs to solve the **visibility problem** — determining which surfaces are in front of others from the camera's point of view. This is typically handled using a **depth buffer** (also called a z-buffer), which records the distance from the camera to the nearest surface drawn at each pixel so far, ensuring that closer objects correctly obscure farther ones regardless of the order they're drawn in.

## 9.3 Mixing 2D And 3D

Many games blend 2D and 3D rendering rather than using one exclusively:

- **2.5D games** use 3D models and environments but constrain gameplay to a 2D plane (common in side-scrolling action games with 3D visuals).
- **UI in 3D games** is almost always rendered as 2D elements (health bars, menus, text) layered on top of the 3D scene, since UI generally shouldn't be affected by 3D perspective or lighting.
- **Billboarded sprites** are 2D images placed within a 3D world that always rotate to face the camera, commonly used for particle effects, distant foliage, or simple decorative elements where a full 3D model would be wasteful.

Most engines support both 2D and 3D rendering paths simultaneously, letting developers combine techniques as needed rather than being forced to pick one exclusively for an entire project.

## 9.4 Choosing a Rendering Approach

Whether a game should lean on 2D or 3D rendering depends heavily on the kind of experience being built:

- **2D rendering** tends to be cheaper computationally, faster to produce art for (no 3D modeling or rigging required), and well-suited to genres like platformers, puzzle games, and classic RPGs.
- **3D rendering** allows for depth, dynamic camera angles, and more immersive environments, but comes with significantly higher art and technical production costs — 3D models, rigging, and animation (covered in Lesson 14) all require specialized skills and more time.

Many successful indie teams deliberately choose 2D specifically because it lets a small team produce a polished, complete game without needing a large 3D art pipeline — a practical decision covered further in Lesson 19 when we discuss matching engines and rendering approaches to project needs.

---

[Previous](./[8]-Materials-Shaders-And-Lighting.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[10]-Physics-Engines.md)
