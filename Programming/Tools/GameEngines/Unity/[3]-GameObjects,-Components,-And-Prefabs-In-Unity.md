[Previous](./[2]-The-Unity-Editor-Interface.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[4]-Introduction-To-C%23-Scripting-In-Unity.md)

*Core Concepts And Scripting*

# Lesson 3 - GameObjects, Components, And Prefabs In Unity

## 3.1 GameObjects As Containers

A **GameObject** is Unity's basic entity — but unlike Godot's nodes, a GameObject has almost no behavior of its own. On its own, a GameObject is essentially an empty container with just a name, a position in the Hierarchy, and a `Transform`. All actual functionality — rendering a sprite, playing a sound, responding to physics, running your own game logic — comes from **Components** attached to it.

```
Player (GameObject)
├── Transform          (built-in, always present)
├── Sprite Renderer     (draws an image)
├── Rigidbody2D          (physics simulation)
├── Box Collider 2D       (collision shape)
└── PlayerController      (your own script)
```

This is Unity's core architectural idea, and it's different enough from Godot's node system to be worth stating plainly: **a Godot node already knows how to do one specific thing** (a `Sprite2D` draws, an `AudioStreamPlayer` plays sound), while **a Unity GameObject does nothing until you assemble behavior onto it** from a set of independent Components.

---

## 3.2 Adding And Configuring Components

Components are added via the Inspector's **Add Component** button (or right-click > a GameObject in the Hierarchy), searchable by name:

```
[Add Component] → search "Rigidbody" → Rigidbody2D
```

Once added, a Component's fields appear directly in the Inspector, editable without code:

- **Rigidbody2D** — `Mass`, `Gravity Scale`, `Body Type` (Dynamic/Kinematic/Static).
- **Sprite Renderer** — `Sprite`, `Color`, `Sorting Layer`.
- **Box Collider 2D** — `Size`, `Offset`, `Is Trigger`.

Multiple Components can be freely combined on a single GameObject — a `Player` might have a `Rigidbody2D` for physics, a `SpriteRenderer` for visuals, an `Animator` for animation state, and several of your own scripts, all working together on the same object. This "mix and match small pieces" approach is what makes Components reusable across very different GameObjects — the same `Rigidbody2D` component works identically whether it's attached to a player, an enemy, or a physics-driven crate.

---

## 3.3 Prefabs And Prefab Variants

A **Prefab** is a saved, reusable GameObject (including its full Component setup and any children) — Unity's direct equivalent of a saved `.tscn` scene in Godot. Once you've built a GameObject the way you want it — say, a fully configured `Enemy` with a sprite, collider, and AI script — you drag it from the Hierarchy into the Project window to save it as a `.prefab` asset.

```
Assets/Prefabs/
├── Enemy.prefab
├── Player.prefab
└── Bullet.prefab
```

Editing the original Prefab asset propagates that change to **every instance of it** placed across every scene in the project — fix a bug in `Enemy.prefab` once, and every level using that enemy is fixed automatically, exactly like editing a reused Godot scene.

**Prefab Variants** extend this further: a variant is a Prefab based on another Prefab, inheriting everything from its parent but allowed to override specific properties. A `FastEnemy` variant of `Enemy.prefab`, for example, could override just the movement speed while still automatically receiving any other changes made to the base `Enemy.prefab` later — useful for building families of related objects without duplicating an entire setup.

---

## 3.4 Instantiating Prefabs At Runtime

Placing Prefabs by hand in the editor works for level layout, but most gameplay needs to spawn objects while the game is running — bullets, spawned enemies, pickups. This is done in C# with `Instantiate()`:

```csharp
using UnityEngine;

public class EnemySpawner : MonoBehaviour
{
    public GameObject enemyPrefab;   // assigned in the Inspector
    public Transform spawnPoint;

    public void SpawnEnemy()
    {
        Instantiate(enemyPrefab, spawnPoint.position, Quaternion.identity);
    }
}
```

Here, `enemyPrefab` is a **public field** (covered fully in Lesson 4.2), letting you drag the actual `Enemy.prefab` asset onto this script directly in the Inspector rather than hardcoding a reference in code. `Instantiate()` creates a live copy of the Prefab in the current scene at the given position and rotation (`Quaternion.identity` means "no rotation").

Removing a spawned or placed GameObject works the same way in reverse:

```csharp
Destroy(gameObject);          // destroys this GameObject
Destroy(gameObject, 2.0f);    // destroys it after a 2 second delay
```

This spawn-via-`Instantiate()` / remove-via-`Destroy()` pair is the standard pattern for anything created and removed dynamically during play — bullets fired by the player, enemies spawned by a wave system, or temporary visual effects.

[Previous](./[2]-The-Unity-Editor-Interface.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[4]-Introduction-To-C%23-Scripting-In-Unity.md)
