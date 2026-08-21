[Previous](./[15]-Tilemaps-and-Level-Design.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[17]-Cameras-in-2D.md)

*2D Game Development*

# Lesson 16 - 2D Physics & Collisions

## 16.1 Physics Bodies in 2D

To participate in physics simulation, an object needs a physics body component, typically one of:

- **Static body** — never moves (walls, floors); other objects collide against it, but it doesn't respond to forces.
- **Dynamic/Rigidbody** — fully simulated, affected by gravity and forces (a ball, a falling crate).
- **Kinematic body** — moved directly through code rather than physics forces, but can still push and be detected by other physics objects (often used for player-controlled characters, covered further in Lesson 24).

---

## 16.2 Colliders

A **collider** defines the physical shape used for collision detection, which is often simpler than the object's visual sprite for performance reasons:

- **Box collider** — a rectangle, good for platforms and crates.
- **Circle collider** — a circle, good for balls and rounded characters; cheaper to compute than a box.
- **Polygon collider** — a custom shape matching irregular sprites more precisely.

A collider does not need to match the sprite's shape exactly — a slightly simplified, generous shape often *feels* better to players than a pixel-perfect one, since exact edges can make near-misses feel unfair.

---

## 16.3 Collision Detection vs Collision Response

- **Collision detection** is the engine noticing that two colliders are overlapping or touching.
- **Collision response** is what the engine (and your code) does about it — stopping movement, applying bounce, dealing damage, playing a sound.

Engines expose collision events your scripts can respond to, commonly named something like `OnCollisionEnter`, `OnCollisionExit`, and `OnCollisionStay`, firing when contact begins, ends, and continues respectively.

---

## 16.4 Triggers vs Solid Collisions

- **Solid collisions** physically block movement — two solid colliders can't pass through each other.
- **Triggers** detect overlap *without* physically blocking movement — used for things like pickups, checkpoints, or damage zones, where you want to know an overlap happened but don't want the objects to bounce off each other.

```csharp
void OnTriggerEnter2D(Collider2D other) {
    if (other.CompareTag("Player")) {
        CollectCoin();
    }
}
```

Choosing trigger vs. solid correctly is one of the most common early sources of 2D physics bugs — a coin pickup accidentally set to solid will physically block the player instead of being collected.

[Previous](./[15]-Tilemaps-and-Level-Design.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[17]-Cameras-in-2D.md)
