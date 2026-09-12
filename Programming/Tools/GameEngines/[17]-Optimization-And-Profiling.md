[Previous](./[16]-Asset-Pipelines-And-Resource-Management.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[18]-Building-And-Deploying-Games.md)

*Performance And Production*

# Lesson 17 - Optimization And Profiling

## 17.1 Why Optimization Matters

A game's rendering loop (Lesson 7) needs to produce a new frame fast enough to maintain a smooth frame rate — commonly 30 or 60 frames per second, meaning every single frame must be fully simulated, rendered, and displayed in 33 or 16.6 milliseconds respectively. Any system that takes longer than its share of that budget — physics, scripting, rendering, audio — causes the whole frame to run late, which the player perceives as **stuttering** or **lag**.

Optimization is the process of finding and reducing these costs so the game consistently hits its target frame rate across the range of hardware it needs to support. It's rarely something that can be fully solved once, up front, purely through good architecture — real games are optimized iteratively throughout development, especially as more content (more objects, more effects, more AI) gets added late in a project and starts pushing against the frame budget established earlier.

A useful mindset: optimization is not about making everything as fast as theoretically possible, but about finding *where the time is actually going* and fixing the parts that matter, since a game can be shipped successfully with plenty of inefficient code, as long as none of it is on the "critical path" of the frame.

---

## 17.2 Profiling Tools

Guessing where a game's performance problems come from is notoriously unreliable — the part of the code a developer *assumes* is slow is often not the actual bottleneck. **Profiling tools**, built into essentially every modern engine, solve this by directly measuring how long each part of a frame actually takes.

A typical profiler breaks a single frame down into a timeline of categories, such as:

```
Frame time: 22ms (target: 16.6ms)
├── Scripting/Gameplay logic:  9ms
├── Physics simulation:        4ms
├── Rendering:                 7ms
└── Audio:                     2ms
```

From output like this, a developer can immediately see that scripting logic is the largest single contributor to this frame running over budget, and focus their optimization effort there rather than, say, needlessly trying to speed up audio (which is already a small fraction of the frame). Profilers can typically drill down further within each category too — for example, showing which individual script or which individual draw call is the most expensive — turning "the game feels slow" into a specific, actionable target.

---

## 17.3 Common Performance Bottlenecks

While every game is different, a handful of performance problems recur constantly across projects:

- **Too many draw calls** — each individual object the renderer draws carries some fixed overhead beyond just the pixels it produces; a scene with thousands of small separate objects can be slower to render than a scene with far more total geometry combined into fewer, larger pieces.
- **Expensive physics or collision checks** — naively checking every object against every other object for collisions (Lesson 11) scales very poorly as object count grows, which is exactly why broad-phase techniques exist.
- **Unoptimized scripts running every frame** — logic in a lifecycle method like `update()` (Lesson 13) runs constantly, so an expensive operation placed there (searching through a large list, for instance) multiplies its cost by every frame it runs, whereas the same operation running once at a specific trigger might be negligible.
- **Uncontrolled memory allocation** — repeatedly creating and destroying objects at runtime, rather than pooling them (Lesson 16), can cause frame-time spikes as the underlying memory system does extra work to find and organize free space.

Recognizing these patterns lets a developer often guess *reasonably* where to look even before profiling, though the profiler remains the tool that confirms whether a suspected bottleneck is actually the one costing real time in a specific game.

| Symptom | Likely bottleneck | Common fix |
|---|---|---|
| Frame rate drops in crowded scenes | Too many draw calls | Batch objects, reduce unique materials |
| Stutter when many objects collide | Expensive collision checks | Simplify colliders, use broad phase |
| Slowdown that gets worse over time | Unoptimized `update()` logic, or a memory leak | Profile per-frame cost, check reference counts |
| Hitches when spawning/destroying objects | Uncontrolled memory allocation | Use object pooling |

---

## 17.4 Level Of Detail (LOD)

**Level of detail (LOD)** is an optimization technique built specifically around a simple observation: an object far from the camera occupies very few pixels on screen, so rendering it with the same detail as an object right in front of the camera wastes processing time the player will never actually perceive.

Engines implement this by preparing multiple versions of the same model, at decreasing detail, and automatically swapping between them based on distance from the camera:

```
LOD 0 (distance 0–10m):   50,000 triangles  (full detail, close-up)
LOD 1 (distance 10–40m):  8,000 triangles   (reduced detail)
LOD 2 (distance 40m+):    800 triangles     (silhouette only)
```

As the player moves closer or farther from an object, the engine swaps the active mesh between these LOD levels, ideally at a distance where the reduction in detail isn't consciously noticeable. This same idea extends beyond just geometry — texture resolution, shadow quality, and even animation update frequency are commonly reduced for distant objects using the same principle, collectively allowing a scene with an enormous amount of total content to stay within its rendering budget by only spending full detail on what's actually close enough to matter.

---

[Previous](./[16]-Asset-Pipelines-And-Resource-Management.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[18]-Building-And-Deploying-Games.md)
