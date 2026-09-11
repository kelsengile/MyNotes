[Previous](./[1]-What-Is-A-Game-Engine.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[3]-Popular-Game-Engines.md)

*Engine Foundations*

# Lesson 2 - Core Engine Architecture

## 2.1 The Game Loop

Almost every real-time game is built around a **game loop** — a cycle that runs continuously while the game is active, typically many times per second. Each pass through the loop generally does three things: read input, update the game's state (logic, physics, AI), and render a frame to the screen.

A simplified game loop looks like this in pseudocode:

```
initialize()

while (game_is_running) {
    input = poll_input()
    update(input, delta_time)
    render()
}

shutdown()
```

The `delta_time` value — the time elapsed since the last frame — is critical. Without it, a game would run faster on powerful hardware and slower on weak hardware, since more loop iterations would happen per second. By multiplying movement and physics calculations by `delta_time`, a character moves at the same real-world speed regardless of how fast the computer is rendering frames.

Engines build much more sophisticated versions of this loop internally — separating fixed-rate updates (used for physics, so simulations stay stable and deterministic) from variable-rate updates (used for rendering, which can run as fast as the hardware allows).

## 2.2 Engine Subsystems Overview

An engine is really a set of cooperating subsystems, each responsible for one concern. The most common ones include:

- **Rendering** — draws the game world to the screen.
- **Physics** — simulates gravity, collisions, and movement of physical objects.
- **Audio** — plays sound effects and music, often with 3D positioning.
- **Input** — reads keyboard, mouse, controller, or touch input.
- **Scripting** — runs the game-specific logic written by developers.
- **Animation** — moves characters and objects over time according to defined motions.
- **Resource/Asset management** — loads and unloads textures, models, sounds, and other files.
- **Networking** (in many engines) — synchronizes game state across multiple connected players.

These subsystems don't operate in isolation — the physics system needs to tell the rendering system where objects moved, the input system needs to feed the scripting system, and so on. A large part of engine design is defining clean ways for these systems to communicate without becoming tightly tangled together.

## 2.3 Runtime vs Editor

Engines are generally split into two related but distinct pieces of software:

- The **runtime** is the code that actually ships with the finished game — the rendering, physics, audio, and logic systems that run on a player's device. It has no editing tools; it just executes the game.
- The **editor** is the development-time application used to build the game — placing objects, adjusting properties, previewing scenes, and packaging the final build. The editor typically embeds a copy of the runtime so you can hit "play" and test the game live inside it.

This separation matters because the runtime needs to be lean and fast (it's what players actually experience), while the editor can be as feature-rich and resource-heavy as needed to support development, since it never ships to players.

## 2.4 Data-Driven Design

Modern engines favor a **data-driven** approach: as much of the game as possible is described in data (scene files, configuration files, tables of numbers) rather than hardcoded in program source code. A character's health, speed, and attack damage, for example, are usually stored as editable fields on a data asset rather than written as literal numbers buried in code.

This matters for a few reasons:

- **Designers, not just programmers, can tune the game** — adjusting a value in the editor doesn't require recompiling any code.
- **Faster iteration** — changing a data file can often be reloaded instantly, while changing code may require a rebuild.
- **Reusability** — the same piece of logic (e.g., "an enemy that patrols and attacks") can be reused for many different enemy types just by swapping out the data that drives it.

Data-driven design is one of the biggest reasons engines feel powerful once mastered: instead of writing a new program for every character or level, you write general-purpose systems once, then configure them endlessly through data.

---

[Previous](./[1]-What-Is-A-Game-Engine.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[3]-Popular-Game-Engines.md)
