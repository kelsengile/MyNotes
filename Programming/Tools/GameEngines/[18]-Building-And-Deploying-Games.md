[Previous](./[17]-Optimization-And-Profiling.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[19]-Choosing-A-Game-Engine.md)

*Performance And Production*

# Lesson 18 - Building And Deploying Games

## 18.1 Build Targets And Platforms

While developing a game, most work happens inside the engine's own editor (Lesson 2), which runs on the developer's machine and provides live debugging, hot-reloading, and inspection tools. Eventually, though, the game needs to be turned into a standalone product the player can actually run — this process is called **building**, and the specific platform it's built for (Windows, macOS, a game console, a mobile device, a web browser) is its **build target**.

Each target has its own requirements and constraints: a mobile build needs to fit within tight memory and battery limits, a console build must pass strict certification requirements from the platform holder, and a web build needs to run inside a browser sandbox with no direct file-system access. Modern engines abstract away much of this complexity — the same project can typically be exported to several targets with only build-specific settings changed, rather than rewriting game logic per platform — but some amount of target-specific tuning (asset resolution, control schemes, performance budgets) is almost always necessary regardless of how platform-agnostic the underlying engine is.

## 18.2 Build Configurations

Separate from the target platform, a build is usually also produced in one of a few standard **configurations**, which control how much debugging information and internal tooling are included:

- A **debug build** includes extra checks, logging, and debugging symbols, making it easier to diagnose problems, but running noticeably slower as a result.
- A **development build** sits in between — closer to full performance, but still keeping some diagnostic tools and profiler hooks (Lesson 17) active, since profiling a debug build's inflated performance numbers wouldn't reflect real-world behavior.
- A **release build** (sometimes called a "shipping" or "master" build) strips out debugging tools entirely and applies every available optimization, producing the fastest, smallest version of the game — this is the configuration actually distributed to players.

A team typically works in debug or development builds throughout production, only switching to a release build configuration for final performance testing and the actual shipped product, since debugging a release build is difficult once its diagnostic information has been stripped away.

## 18.3 Packaging Assets

A finished build isn't just compiled game logic — it also needs to bundle every asset the game actually uses: textures, meshes, audio, levels, and more. **Packaging** is the step where the engine gathers all referenced assets, applies their platform-specific import settings (Lesson 16) — such as using a different texture compression format on mobile than on PC — and bundles everything into the final distributable files.

Larger games commonly split packaged assets across multiple files rather than one enormous package, for a few practical reasons:

```
game.exe
├── core.pak       (always loaded: UI, player, core systems)
├── level_01.pak    (loaded only when Level 1 starts)
├── level_02.pak    (loaded only when Level 2 starts)
└── dlc_expansion.pak (optional, only present if DLC is installed)
```

This split maps naturally onto the per-scene loading strategies from Lesson 16 — a level's assets can be packaged into their own file and only loaded from disk when that specific scene is actually needed — and it also enables smaller initial downloads or separately distributed downloadable content (DLC), since players don't need to download package files for content they haven't unlocked or purchased.

## 18.4 Testing Across Platforms

Because a game can be built for many different targets, each with its own hardware quirks, input methods, and performance characteristics, testing on a single platform is rarely enough to catch every issue before release. **Cross-platform testing** deliberately runs the game on the actual range of hardware (or a representative sample of it) it's expected to support, since problems frequently only appear on specific configurations:

- A game might run at a perfectly smooth frame rate on a developer's high-end PC but stutter badly on a lower-end machine or older console — an issue optimization work (Lesson 17) is specifically meant to catch, but only if it's actually tested on that weaker hardware.
- Input handling (Lesson 12) needs to be verified across every supported device — keyboard and mouse, gamepad, touchscreen — since a control scheme that feels natural on one can be awkward or entirely non-functional on another.
- Platform certification requirements (particularly strict on consoles) often mandate specific behaviors — such as correctly handling a controller disconnecting mid-game — that may never come up during normal development testing on a single machine.

This is also where **build automation** becomes valuable at scale: many studios set up systems that automatically produce fresh builds for every target on a regular schedule (nightly, for instance), so that testers always have an up-to-date build to check across the full range of supported platforms, rather than testing being blocked on someone manually producing a new build first.

---

[Previous](./[17]-Optimization-And-Profiling.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[19]-Choosing-A-Game-Engine.md)
