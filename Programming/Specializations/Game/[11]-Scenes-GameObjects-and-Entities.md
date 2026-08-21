[Previous](./[10]-The-Game-Loop.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[12]-Component-Based-Architecture.md)

*Game Engine Fundamentals*

# Lesson 11 - Scenes, GameObjects & Entities

## 11.1 What Is a Scene?

A **scene** (called a Level in some engines, a Map in others) is a self-contained space containing everything needed for one part of your game — a menu screen, a level, a boss arena. Scenes can be loaded, unloaded, and swapped at runtime, which is how a game transitions from a main menu into gameplay, or from one level to the next.

---

## 11.2 GameObjects / Entities / Nodes

Every engine uses its own term for "a thing that exists in a scene":

- **Unity**: GameObject
- **Unreal**: Actor
- **Godot**: Node

By itself, an empty GameObject/Actor/Node does nothing — it's just a position in space. Behavior comes from attaching components, scripts, meshes, colliders, and other pieces to it (covered in Lesson 12).

---

## 11.3 Parent-Child Hierarchies

Objects can be nested inside one another, forming a hierarchy:

- A child object's position, rotation, and scale are relative to its parent.
- Moving a parent moves all of its children along with it.
- This is used constantly — for example, a weapon object parented to a character's hand bone will follow the hand automatically as the character animates.

Understanding local vs. world position within a hierarchy connects directly to transforms, covered in Lesson 13.

---

## 11.4 Instantiating and Destroying Objects

Games rarely place every object by hand in the editor — most are created and removed dynamically at runtime:

```csharp
GameObject bullet = Instantiate(bulletPrefab, spawnPosition, spawnRotation);
// ... later
Destroy(bullet, 2f); // destroy after 2 seconds
```

- **Instantiating** creates a new copy of a template object (a "Prefab" in Unity, a "Scene" or "PackedScene" in Godot, a "Blueprint" in Unreal) at runtime — used for bullets, enemies, pickups, and particle effects.
- **Destroying** removes an object from the scene and frees its memory. Forgetting to destroy objects that are no longer needed (like off-screen bullets) causes a **memory leak** and gradually degrades performance — an important habit to build early.

[Previous](./[10]-The-Game-Loop.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[12]-Component-Based-Architecture.md)
