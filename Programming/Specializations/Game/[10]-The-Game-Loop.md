[Previous](./%5B9%5D-Collections-and-Data-Structures.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[11]-Scenes-GameObjects-and-Entities.md)

*Game Engine Fundamentals*

# Lesson 10 - The Game Loop

## 10.1 What Is a Game Loop?

At its core, every game is one continuously repeating loop:

```
while (gameIsRunning) {
    ProcessInput();
    Update();
    Render();
}
```

This loop runs many times per second. Game engines handle the loop for you and expose specific callback functions (`Update`, `_process`, etc.) that you fill in with your own gameplay code, rather than writing the raw `while` loop yourself.

---

## 10.2 Update, Render, and Fixed Update

- **Update** runs once per rendered frame and handles most gameplay logic: input reading, timers, animation triggers.
- **Render** draws the current state of the world to the screen — usually handled entirely by the engine.
- **FixedUpdate** (or `_physics_process`) runs on a fixed, consistent timestep (e.g. exactly every 0.02 seconds), independent of how fast frames are rendering. Physics calculations belong here, because physics needs a stable, predictable timestep to behave consistently.

A common mistake is putting physics-affecting code (like `AddForce`) inside `Update` instead of `FixedUpdate` — this can cause inconsistent physics behavior at different frame rates.

---

## 10.3 Delta Time

**Delta time** is the amount of time that passed since the last frame. Multiplying movement by delta time makes motion **frame-rate independent**:

```csharp
// Wrong: moves faster on high-framerate machines, slower on low-framerate ones
transform.position += speed * direction;

// Correct: moves at the same real-world speed regardless of frame rate
transform.position += speed * direction * deltaTime;
```

Without delta time, a game would run at different effective speeds on different hardware — a serious bug for any game that leaves the developer's own machine.

---

## 10.4 Frame Rate and Frame Independence

**Frame rate** (measured in FPS, frames per second) is how many times the loop runs per second. A stable, high frame rate keeps a game feeling responsive; drops or spikes ("stuttering") are noticeable and unpleasant to players. Two closely related targets to understand:

- **Target frame rate** — the FPS a game aims to maintain (commonly 30 or 60, though this varies by platform and genre).
- **VSync** — synchronizes rendering with the monitor's refresh rate to prevent visual tearing, at the cost of sometimes capping your frame rate below what the hardware could otherwise achieve.

Designing gameplay code around delta time (rather than assuming a fixed frame rate) is one of the most important habits to build early, since it affects almost every system covered later in this course.

[Previous](./%5B9%5D-Collections-and-Data-Structures.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[11]-Scenes-GameObjects-and-Entities.md)
