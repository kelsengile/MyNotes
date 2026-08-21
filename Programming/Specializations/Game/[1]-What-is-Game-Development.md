[Previous](./[0]-Introduction-to-Game-Development.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[2]-Setting-Up-a-Game-Engine.md)

*Getting Started*

# Lesson 1 - What is Game Development? Genres & Engines Overview

## 1.1 What Is Game Development?

Game development is the process of designing, building, and shipping an interactive experience. Unlike a static app, a game constantly loops: it reads player input, updates its internal state (the "simulation"), and draws the result to the screen, many times per second. Building one touches several disciplines at once:

- **Design** — deciding what the game is, how it plays, and why it's fun.
- **Programming** — writing the logic that runs the simulation and responds to input.
- **Art & Audio** — creating the visuals, animations, sound effects, and music.
- **Production** — planning scope, managing tasks, and shipping on time.

Most solo developers and small teams wear several of these hats at once. This course focuses mainly on the programming and technical side, using a modern game engine as the foundation.

---

## 1.2 Common Game Genres

Genres shape which systems you'll need to build. Some common ones:

- **Platformer** — precise 2D movement, jumping, and level traversal (e.g. side-scrollers).
- **Top-down / Adventure** — exploration-driven games viewed from above or at an angle.
- **First/Third-Person Shooter** — 3D movement, aiming, and combat systems.
- **Puzzle** — rule-based systems with clear win/lose states, often minimal physics.
- **RPG** — stats, inventories, dialogue, and progression systems.
- **Strategy/Simulation** — many interacting entities, AI, and resource management.

Knowing your genre early helps you decide which lessons in this course matter most for your first project.

---

## 1.3 Popular Game Engines (Unity, Unreal, Godot)

A **game engine** is a pre-built framework that already solves the hard, repetitive problems (rendering, physics, input, audio) so you can focus on your game's logic and content.

| Engine | Language(s) | Strengths | Typical Use |
|---|---|---|---|
| **Unity** | C# | Huge asset store, strong 2D and 3D support, large community | Mobile, indie 2D/3D, prototyping |
| **Unreal Engine** | C++ / Blueprints (visual scripting) | High-end 3D rendering, AAA-grade tools | 3D, high-fidelity visuals, larger teams |
| **Godot** | GDScript (Python-like) / C# | Free, open-source, lightweight | 2D games, indie projects, learning |

None of these is objectively "best" — the right choice depends on your genre, target platform, and preferred language.

---

## 1.4 Choosing the Right Engine for Your Project

A few practical questions to guide your choice:

- **What's your target platform?** (PC, mobile, console — all three engines above support multiple platforms, but with different levels of polish.)
- **Do you prefer writing code or using visual scripting?** Unreal's Blueprints let you build logic without traditional code; Unity and Godot lean more code-first.
- **Is your game mostly 2D or 3D?** Godot and Unity both have mature 2D workflows; Unreal is heavily optimized for 3D.
- **What does your team already know?** If you or your collaborators already know C#, Unity is a natural fit. If you know C++, Unreal.

This course explains concepts in an engine-agnostic way wherever possible, while noting engine-specific terms so you can follow along in whichever engine you choose.

[Previous](./[0]-Introduction-to-Game-Development.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[2]-Setting-Up-a-Game-Engine.md)
