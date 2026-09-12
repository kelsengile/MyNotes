[Previous](./[6]-Transforms-And-Spatial-Hierarchy.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[8]-Materials-Shaders-And-Lighting.md)

*Rendering*

# Lesson 7 - Introduction To Rendering

## 7.1 What Is a Renderer

The **renderer** is the engine subsystem responsible for turning a scene's data — objects, their transforms, materials, lights, and a camera — into a 2D image that gets displayed on screen, many times per second. Rendering is one of the most computationally demanding parts of a game engine, which is why engines dedicate significant effort to making it as fast as possible, often relying heavily on the **GPU** (Graphics Processing Unit), a piece of hardware built specifically to perform the massive number of parallel calculations rendering requires.

At a high level, rendering answers one question, repeated for every pixel on the screen: "what color should this pixel be, given everything in the scene?"

---

## 7.2 The Rendering Pipeline

The **rendering pipeline** is the sequence of steps a renderer performs to go from raw scene data to a finished image. While the exact steps vary by engine and graphics API, a simplified pipeline generally includes:

1. **Vertex processing** — the corners ("vertices") of every 3D model are transformed from their local positions into their correct positions on screen, using the transform matrices covered in Lesson 6.
2. **Rasterization** — the GPU figures out which pixels on screen each triangle (made up of three vertices) actually covers.
3. **Fragment/pixel processing** — for every covered pixel, the renderer calculates its final color, factoring in textures, lighting, and materials (covered in Lesson 8).
4. **Output merging** — the final colors are written to the frame buffer (the image that will actually be displayed), taking into account which objects are in front of others.

This pipeline runs for every single frame of the game — often 30, 60, or even more times per second — which is why efficient rendering is such a central concern in engine design.

**Example:** rendering a single triangle-based cube (12 triangles, 8 vertices) walks through the pipeline like this:

```
1. Vertex processing:    8 vertices transformed into screen positions
2. Rasterization:        GPU determines which pixels each of the 12 triangles covers
3. Fragment processing:  every covered pixel gets a final color (texture + lighting)
4. Output merging:       colors written to the frame buffer, respecting depth order
```

Now imagine a scene with a thousand such cubes, all repeating this same four-step process, 60 times a second — that's the kind of scale a GPU is built to handle in parallel.

---

## 7.3 Cameras And Viewports

A scene can't be rendered without a **camera** — the object that defines the position, angle, and field of view from which the scene is observed. Just like a real camera, a game camera has properties such as:

- **Field of view (FOV)** — how wide an angle the camera captures; a wider FOV shows more of the scene but with more distortion at the edges.
- **Near and far clip planes** — the minimum and maximum distance from the camera at which objects are rendered; anything closer than the near plane or farther than the far plane is simply not drawn.
- **Projection type** — most commonly **perspective** (objects farther away appear smaller, mimicking human vision — used in most 3D games) or **orthographic** (objects stay the same size regardless of distance — commonly used for 2D games, UI, and some strategy games).

The **viewport** is the rectangular region of the screen (or window) that the camera's output is drawn into. Most games use a single full-screen viewport, but split-screen multiplayer games use multiple viewports — one camera and viewport per player, each rendering to a portion of the screen.

| Projection | Objects farther away | Typical use case |
|---|---|---|
| Perspective | Appear smaller | First-person shooters, open-world 3D games |
| Orthographic | Stay the same size | 2D platformers, isometric strategy games, UI |

---

## 7.4 Frame Rate And The Render Loop

**Frame rate**, measured in frames per second (FPS), describes how many complete images the renderer produces every second. A higher frame rate generally means smoother, more responsive-feeling motion — 60 FPS is a common target for many genres, while competitive or fast-paced games often aim even higher.

The render loop ties directly back to the game loop from Lesson 2: on every iteration of the game loop, after game logic and physics have updated the state of the world, the renderer draws the current frame based on that updated state. If rendering takes longer than the time budget for a single frame (for example, more than roughly 16.6 milliseconds for a 60 FPS target), the frame rate drops, which players typically perceive as "lag" or "stutter" even if the game's logic itself is running perfectly fine.

Balancing visual quality against frame rate is one of the most constant tensions in real-time rendering — the more detailed the lighting, shadows, and geometry (covered in Lesson 8 and Lesson 9), the more work the GPU has to do for every single frame.

---

[Previous](./[6]-Transforms-And-Spatial-Hierarchy.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[8]-Materials-Shaders-And-Lighting.md)
