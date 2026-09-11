[Previous](./[4]-Scenes-And-The-Game-World.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[6]-Transforms-And-Spatial-Hierarchy.md)

*World And Object Management*

# Lesson 5 - GameObjects And Components

## 5.1 The GameObject Concept

A **GameObject** (sometimes called an "actor," "node," or "entity" depending on the engine) is the basic building block used to represent anything that exists in a scene — a player, an enemy, a light, a camera, a piece of UI, or even an invisible object used purely for logic. On its own, a bare GameObject usually does almost nothing; it's just a container that exists at a position in the world.

What gives a GameObject its behavior and appearance is the set of pieces attached to it — which brings us to components.

## 5.2 Component-Based Architecture

Instead of giving each object type its own dedicated class (a `Player` class, an `Enemy` class, a `Door` class, each written from scratch), most modern engines use **component-based architecture**: a GameObject is built by attaching small, focused, reusable **components**, each responsible for one piece of functionality.

For example, a player character might be assembled from:

- A `SpriteRenderer` (or `MeshRenderer`) component — draws its appearance.
- A `Collider` component — defines its physical shape for collision detection.
- A `Rigidbody` component — makes it subject to physics like gravity.
- A `PlayerMovement` script component — reads input and moves the character.
- An `AudioSource` component — plays footstep and jump sounds.

None of these components need to know about each other's internal details. The `PlayerMovement` script simply moves the GameObject's position, and the `Rigidbody`, `Collider`, and `SpriteRenderer` each react appropriately, because they all operate on the same shared object.

This is powerful because the exact same components can be reused across wildly different objects — a `Rigidbody` and `Collider` work identically whether they're attached to a player, a crate, or a rolling boulder.

## 5.3 Entity-Component-System (ECS)

**Entity-Component-System (ECS)** is a stricter, performance-oriented variation of component-based design, used heavily in engines that need to simulate very large numbers of objects efficiently (for example, thousands of particles, units, or NPCs at once).

ECS separates three concerns that are normally blended together:

- **Entities** — simple identifiers (often just an ID number) representing "a thing that exists." An entity itself holds no data or behavior.
- **Components** — pure data, with no behavior attached (e.g., a `Position` component that just stores x/y/z numbers, with no methods).
- **Systems** — functions that operate on all entities that have a particular combination of components (e.g., a `MovementSystem` that reads every entity with both a `Position` and `Velocity` component and updates its position each frame).

The benefit of ECS is performance: because components are stored as tightly packed arrays of raw data rather than scattered across many individual objects, systems can process thousands of entities very quickly, taking advantage of how modern CPUs access memory. The trade-off is that ECS code can feel less intuitive at first compared to attaching components directly to objects, since behavior lives in separate systems rather than "inside" the object itself.

## 5.4 Composition Over Inheritance

Both component-based architecture and ECS reflect a broader software design principle called **composition over inheritance**. In a traditional inheritance-based approach, you might create a `FlyingEnemy` class that inherits from `Enemy`, which inherits from `Character`. This quickly becomes rigid: what happens when you need an enemy that flies *and* swims, but the class hierarchy only allows one parent?

Composition solves this by building behavior out of small, independent, swappable pieces instead of rigid class hierarchies. A "flying, swimming enemy" is just an enemy GameObject with both a `FlyMovement` component and a `SwimMovement` component attached — no awkward multiple-inheritance problem, and no need to create a brand-new class for every possible combination of traits.

This is one of the most important mental shifts when learning to think in terms of game engines: instead of asking "what class should this object be?", you ask "what components does this object need?"

---

[Previous](./[4]-Scenes-And-The-Game-World.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[6]-Transforms-And-Spatial-Hierarchy.md)
