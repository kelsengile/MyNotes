[Previous](./[39]-Asset-Pipelines-and-Import-Workflows.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[41]-Testing-and-Debugging-Games.md)

*Polishing & Shipping*

# Lesson 40 - Performance Optimization for Games

## 40.1 Profiling Before Optimizing

**Profiling** measures where a game is actually spending its time and memory, using tools built into the engine. The core rule of optimization is to always profile first — intuition about what's "probably slow" is frequently wrong, and optimizing the wrong system wastes effort while leaving the real bottleneck untouched.

---

## 40.2 CPU vs. GPU Bottlenecks

A game is only as fast as its slowest stage, so it's important to know which side is limiting performance:

- **CPU-bound** — the processor can't prepare frames fast enough, often due to game logic, physics, AI, or too many individual draw calls being issued.
- **GPU-bound** — the graphics card can't render frames fast enough, often due to high-resolution textures, expensive shaders, or too many on-screen pixels/effects (overdraw).

A profiler will typically show which one is the current limiting factor, which determines what kind of optimization is actually worth doing.

---

## 40.3 Common Optimization Techniques

- **Object pooling** — reusing a fixed set of pre-created objects (e.g. bullets, enemies) instead of constantly creating and destroying them, which is expensive.
- **Level of detail (LOD)** — swapping a model for a simpler, lower-polygon version as it gets farther from the camera, since fine detail is invisible at a distance anyway.
- **Culling** — skipping rendering (or simulation) for objects that aren't currently visible or relevant, such as **frustum culling** (outside the camera's view) or **occlusion culling** (hidden behind other objects).
- **Batching** — combining multiple draw calls into fewer, larger ones, since each individual draw call has CPU overhead regardless of how small the object is.

---

## 40.4 Memory Management

Beyond raw speed, games need to manage memory carefully to avoid stutters and crashes:

- **Garbage collection pauses** — in garbage-collected languages (like C#), frequent small memory allocations during gameplay can trigger collection pauses that cause visible hitches; minimizing allocations in hot code paths (like the update loop) avoids this.
- **Asset streaming** — loading and unloading assets on demand rather than keeping an entire large world in memory at once.
- **Memory budgets** — especially on consoles and mobile, working within a fixed memory ceiling rather than assuming unlimited RAM, since target hardware is fixed and known in advance.

[Previous](./[39]-Asset-Pipelines-and-Import-Workflows.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[41]-Testing-and-Debugging-Games.md)
