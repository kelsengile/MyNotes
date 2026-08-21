[Previous](./[11]-Scenes-GameObjects-and-Entities.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[13]-Transforms-Coordinates-and-Vectors.md)

*Game Engine Fundamentals*

# Lesson 12 - Components & Component-Based Architecture

## 12.1 What Is a Component?

A component is a self-contained piece of behavior or data that gets attached to a GameObject/Actor/Node. Rather than one object having a single monolithic script, you compose it from smaller pieces:

- A `SpriteRenderer` component draws a 2D image.
- A `Collider` component defines a physical shape for collisions.
- A custom `HealthComponent` script tracks and manages health.

An object's final behavior emerges from *which components are attached to it*, not from what class it inherits from.

---

## 12.2 Entity-Component Pattern

In this pattern, an "entity" (the GameObject/Actor/Node) is essentially a container, and components attached to it define what it does and how it looks. This gives huge flexibility:

- A `Torch` entity might have a `Light` component and a `ParticleEffect` component, but no `Collider`.
- A `Wall` entity might have a `MeshRenderer` and a `Collider`, but no `Light`.

Because components are modular, the same `HealthComponent` can be reused on the player, enemies, and destructible crates alike.

---

## 12.3 Entity-Component-System (ECS)

**ECS** is a stricter, performance-oriented variant of this pattern used in some engines and for large-scale simulations:

- **Entities** are just IDs — they hold no data or logic themselves.
- **Components** are pure data (e.g. a `Position` component holding only x/y/z floats, with no functions).
- **Systems** contain all the logic, and operate on every entity that has a matching set of components (e.g. a `MovementSystem` processes every entity that has both a `Position` and a `Velocity` component).

ECS is more complex to set up than a typical component pattern but can offer significant performance benefits for games with thousands of active objects (e.g. large-scale simulations or strategy games), because it lays data out in memory in a way that's fast for the CPU to process in bulk.

---

## 12.4 Building Behavior from Components

A practical example — building a "Pickup Coin" object from components:

1. A `SpriteRenderer` (or `MeshRenderer`) for the coin's visual.
2. A `Collider` set to "trigger" mode to detect the player overlapping it (Lesson 16).
3. A small `CoinPickup` script that listens for the trigger event, adds to the player's score, plays a sound, and destroys itself.

Each of these pieces is independently reusable — the same trigger-detection approach could be repurposed for a checkpoint, a key, or a damage zone.

[Previous](./[11]-Scenes-GameObjects-and-Entities.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[13]-Transforms-Coordinates-and-Vectors.md)
