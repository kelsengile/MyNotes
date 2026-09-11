[Previous](./[15]-Audio-Systems.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[17]-Optimization-And-Profiling.md)

*Performance And Production*

# Lesson 16 - Asset Pipelines And Resource Management

## 16.1 Importing Assets

Artists and audio designers rarely create content in the exact format an engine uses internally — a 3D model might be authored in a modeling tool like Blender or Maya, a texture painted in Photoshop, a sound recorded and edited in a dedicated audio editor. The **asset pipeline** is the process by which these raw source files are brought into an engine and converted into a format it can efficiently load and use at runtime.

When a raw file is dropped into a project, most engines automatically run an **import** step, which might involve:

- Converting a texture into a compressed, GPU-friendly format.
- Generating simplified collision shapes from a detailed 3D mesh.
- Re-encoding audio into a format optimized for streaming or fast playback.

This importing step usually happens once, up front, rather than every time the game runs — the engine caches the converted result so that a raw source file only needs to be reprocessed when it changes. This is why a project might feel briefly slower the first time it opens or after pulling new art — the engine is (re-)running assets through the import pipeline before it can use them.

## 16.2 Asset Loading Strategies

Even after assets are imported into an engine-friendly format, a full game's worth of assets is typically far too large to hold in memory all at once. Engines generally use one of a few strategies to manage this:

- **Load everything up front** — simple, but only practical for small games; the player waits through a single loading screen before playing, and everything they might need is already in memory.
- **Load per-scene/per-level** — assets for a given scene (Lesson 4) are loaded when it starts and unloaded when it ends. This is the most common approach, and it maps naturally onto the scene loading/unloading behavior already covered there.
- **Streaming** — assets are loaded and unloaded continuously *during* gameplay, based on the player's position, rather than at fixed loading-screen boundaries. Open-world games rely heavily on streaming: only the terrain, buildings, and objects near the player are actually in memory, with distant content streamed in as the player approaches and unloaded again as they move away.

Streaming is the most complex strategy to implement well, since it requires predicting what the player will need *before* they need it (to avoid visible pop-in) while not loading so far ahead that memory is wasted — but it's also what makes seamless, loading-screen-free open worlds possible.

## 16.3 Memory Management

Every asset loaded into memory — a texture, a sound clip, a mesh — takes up a finite amount of RAM and, for graphical assets, often VRAM (video memory) on the GPU as well. Poor memory management is one of the most common sources of instability in shipped games, typically surfacing as slowdowns, stutters, or outright crashes when memory runs out.

Two related concepts sit at the center of this:

- A **reference count** tracks how many parts of the game currently need a given asset in memory. When an object using a texture is destroyed, the engine decrements that texture's reference count; when it reaches zero, the asset becomes eligible to be unloaded.
- A **memory leak** occurs when something keeps a reference to an asset alive long after it's actually needed — for example, a script that loads a texture but never releases its reference even after the object using it is destroyed. Over time, leaked assets accumulate in memory, eventually causing a crash even though the game never appears to load an unreasonable amount of *new* content.

Most engines provide profiling tools (explored further in Lesson 17) specifically to inspect what's currently resident in memory, making it possible to track down exactly which assets are lingering longer than they should.

## 16.4 Object Pooling

Loading and destroying objects has a real, measurable cost — allocating memory, running initialization code, and (for visual objects) triggering the rendering and physics systems to register a new object. This becomes a serious performance problem for objects that are created and destroyed *frequently*, like bullets fired from a rapid-fire weapon, or particles from a repeated explosion effect.

**Object pooling** solves this by avoiding repeated creation and destruction entirely. Instead, a fixed set of objects is created once, up front, and reused:

```
function get_bullet_from_pool() {
    if pool.has_inactive_object():
        bullet = pool.take_inactive_object()
    else:
        bullet = create_new_bullet()  // only if the pool has run out

    bullet.reset(position, direction)
    bullet.set_active(true)
    return bullet
}

function return_bullet_to_pool(bullet) {
    bullet.set_active(false)
    pool.add_inactive_object(bullet)
}
```

Rather than destroying a bullet when it hits something or flies off-screen, the game simply deactivates it and returns it to the pool, ready to be reused the next time a bullet is needed. This trades a small amount of upfront memory (reserving space for objects that might sit inactive for a while) for a significant runtime performance gain, and it's one of the most common optimization techniques applied to any gameplay system involving short-lived, frequently-spawned objects.

---

[Previous](./[15]-Audio-Systems.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[17]-Optimization-And-Profiling.md)
