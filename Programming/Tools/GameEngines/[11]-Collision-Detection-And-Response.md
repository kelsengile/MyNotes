[Previous](./[10]-Physics-Engines.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[12]-Input-Handling.md)

*Physics And Interaction*

# Lesson 11 - Collision Detection And Response

## 11.1 Broad Phase vs Narrow Phase

Detecting collisions between every possible pair of objects in a scene, every single frame, is extremely expensive if done naively — a scene with 1,000 objects would require checking roughly 500,000 pairs per frame just to see which ones might be touching. To make this practical, physics engines split collision detection into two phases:

- **Broad phase** — a fast, approximate pass that quickly rules out pairs of objects that clearly *cannot* be colliding, usually by checking simple bounding boxes rather than exact shapes. This dramatically shrinks the number of pairs that need detailed checking.
- **Narrow phase** — a slower, precise pass that only runs on the much smaller set of object pairs the broad phase identified as *potentially* colliding, using their actual collider shapes to determine exactly whether (and where) they intersect.

This two-phase approach is a classic performance optimization pattern: do a cheap, approximate check first to eliminate the obviously-not-colliding majority, then only spend expensive, precise computation on the few pairs that actually need it.

## 11.2 Collision Shapes

Colliders (introduced in Lesson 10) come in several common shapes, each with different performance and accuracy trade-offs:

- **Box/cube colliders** — cheap to compute and good for rectangular objects like crates or walls.
- **Sphere colliders** — the cheapest shape to check collisions for, ideal for balls or rough approximations of round objects.
- **Capsule colliders** — a cylinder with rounded ends, commonly used for characters since it handles moving over uneven ground and stepping over small obstacles gracefully.
- **Mesh colliders** — use an object's exact (or simplified) 3D geometry, offering the most accuracy but at significantly higher computational cost, and are generally only used for static, non-moving geometry like terrain.

A common best practice is to use the simplest collider shape that reasonably approximates an object, rather than defaulting to expensive mesh colliders everywhere — a detailed character model, for example, is usually given a simple capsule collider rather than a collider matching every finger and strand of hair.

## 11.3 Triggers vs Collisions

Not every overlap between two colliders should result in physical collision response (objects pushing each other apart). Engines distinguish between two modes:

- A **collision** causes physical response — the physics engine prevents the objects from overlapping, applying forces to push them apart, stop movement, or bounce, depending on their physics materials (Lesson 10).
- A **trigger** detects overlap *without* any physical response — the objects simply pass through each other, but the engine still fires an event that scripts can react to. Triggers are commonly used for things like a level exit zone, an item pickup area, or a zone that starts a cutscene when the player walks into it.

Using a trigger for the `LevelExit` object from Lesson 4's scene example is a good illustration: the player should be able to walk *through* the exit zone to trigger the next level, not physically bump into an invisible wall there.

## 11.4 Collision Events

When a collision or trigger overlap occurs, the physics engine notifies relevant scripts through **collision events**, which scripts can respond to using lifecycle methods (covered in more depth in Lesson 13). Common events include:

- **On collision/trigger enter** — fired the moment two colliders begin overlapping.
- **On collision/trigger stay** — fired continuously every frame while two colliders remain overlapping.
- **On collision/trigger exit** — fired the moment two colliders stop overlapping.

A simplified pseudocode example for a coin pickup, using a trigger enter event:

```
function on_trigger_enter(other_object) {
    if (other_object.tag == "Player") {
        player_score += coin_value
        destroy(this_object)
    }
}
```

This pattern — detecting an overlap, checking what kind of object was involved, then reacting accordingly — is one of the most common building blocks in gameplay programming, used for everything from item pickups and damage zones to level triggers and enemy detection ranges.

---

[Previous](./[10]-Physics-Engines.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[12]-Input-Handling.md)
