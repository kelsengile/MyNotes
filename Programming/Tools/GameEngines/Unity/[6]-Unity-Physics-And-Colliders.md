[Previous](./[5]-MonoBehaviour-Lifecycle-Methods.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[7]-The-Unity-UI-System-(Canvas-And-RectTransform).md)

*Building Features*

# Lesson 6 - Unity Physics And Colliders

## 6.1 Rigidbody And Rigidbody2D

A GameObject only participates in physics simulation once it has a `Rigidbody` (3D) or `Rigidbody2D` (2D) component — without one, a Collider on that GameObject is purely static, never moving regardless of collisions, much like an Anchored Part in Roblox or a `StaticBody` in Godot from the earlier Topics.

Key `Rigidbody`/`Rigidbody2D` settings:

| Property | Effect |
|---|---|
| `Body Type` (2D) / `Is Kinematic` (3D) | `Dynamic` — fully simulated by physics. `Kinematic` — moved only by script/animation, but still detected by other physics objects. `Static` (2D only) — never moves, like a Collider with no Rigidbody at all. |
| `Mass` | Affects how forces and collisions influence it |
| `Gravity Scale` (2D) / `Use Gravity` (3D) | Whether gravity affects this body |
| `Drag` / `Angular Drag` | Simulated air resistance, slowing movement/rotation over time |

Moving a `Rigidbody` should be done through physics methods rather than directly setting `transform.position` every frame, since bypassing the physics system can cause it to tunnel through colliders or behave inconsistently:

```csharp
void FixedUpdate()
{
    rb.linearVelocity = new Vector2(moveInput * speed, rb.linearVelocity.y);
    // or, for one-time forces:
    // rb.AddForce(jumpDirection * jumpForce, ForceMode2D.Impulse);
}
```

---

## 6.2 Colliders And Collision Detection Modes

A **Collider** (`BoxCollider2D`, `CircleCollider2D`, `BoxCollider`, `SphereCollider`, `MeshCollider`, etc.) defines a GameObject's physical shape for collision purposes — separate from its visual shape, the same separation of "what you see" vs "what physics reacts to" found in every engine covered in this course series.

```
Player (GameObject)
├── Sprite Renderer      (the visual)
├── Rigidbody2D           (participates in physics)
└── Box Collider 2D        (the actual collision shape, often invisible)
```

Fast-moving objects (bullets, anything that can move far enough in one physics step to pass entirely through a thin collider) may need adjusted **Collision Detection** settings:

- **Discrete** (default) — checks for collisions only at the object's position each step; fast enough for most objects, but can miss thin colliders at high speed ("tunneling").
- **Continuous** — checks along the object's full path of motion during the step, preventing tunneling at the cost of extra performance. Reserve this for genuinely fast-moving objects like bullets, since applying it everywhere is unnecessarily expensive.

---

## 6.3 OnCollisionEnter vs OnTriggerEnter

Unity distinguishes between **solid collisions** (objects physically stop each other) and **triggers** (objects detect overlap but pass through), controlled by a Collider's `Is Trigger` checkbox — and each uses a different set of callback methods:

```csharp
// Is Trigger = false: solid collision
void OnCollisionEnter2D(Collision2D collision)
{
    Debug.Log("Hit: " + collision.gameObject.name);
}

// Is Trigger = true: overlap detection, no physical blocking
void OnTriggerEnter2D(Collider2D other)
{
    if (other.CompareTag("Player"))
    {
        Debug.Log("Player entered the pickup zone");
        Destroy(gameObject);   // collect the pickup
    }
}
```

Both families also have `*Stay` (called every frame while still overlapping) and `*Exit` (called once, when contact ends) variants — `OnCollisionStay2D`, `OnTriggerExit2D`, and so on. Which pair fires depends entirely on the `Is Trigger` setting on the Collider(s) involved, and **at least one of the two colliding objects must have a non-kinematic Rigidbody** for either family of callback to fire at all — a very common source of "my collision code just isn't running" bugs for beginners.

This maps directly onto Godot's distinction from the earlier Topic: Unity's `OnCollisionEnter` corresponds to a solid `CharacterBody`/`RigidBody` collision, while `OnTriggerEnter` corresponds to Godot's `Area2D`/`Area3D` overlap detection.

---

## 6.4 Physics Materials In Unity

**Physics Materials** (`Physics Material 2D` / `Physics Material` for 3D) are reusable assets that define how a surface behaves on contact — friction and bounciness — independent of any single object's script:

| Property | Effect |
|---|---|
| **Friction** | How much a surface resists sliding — low friction (ice) lets things slide freely, high friction (rubber) resists sliding |
| **Bounciness** | How much velocity is retained after a bounce — 0 means no bounce (absorbed), 1 means a perfectly elastic bounce |

```
Assets/Physics Materials/
├── Ice.physicsMaterial2D        (low friction)
├── Rubber.physicsMaterial2D     (high bounciness)
└── Default.physicsMaterial2D
```

You create one via **Assets > Create > 2D > Physics Material 2D**, configure its values, then assign it to any Collider's `Material` field in the Inspector. Because it's a reusable asset rather than a per-object setting, updating `Ice.physicsMaterial2D` once instantly changes the behavior of every Collider in the project using it — the same reuse benefit Prefabs provide for GameObjects, applied to surface physics instead.

[Previous](./[5]-MonoBehaviour-Lifecycle-Methods.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[7]-The-Unity-UI-System-(Canvas-And-RectTransform).md)
