[Previous](./[12]-Input-Handling.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[14]-Animation-Systems.md)

*Gameplay Systems*

# Lesson 13 - Scripting And Game Logic

## 13.1 Scripting Languages In Engines

**Scripting** is how developers write the game-specific behavior that the engine's built-in systems don't provide out of the box — how a character responds to input, how an enemy decides to attack, how score is tracked. Rather than requiring every developer to write in the same low-level language the engine's core is built in, most engines expose a higher-level **scripting language** designed to be more approachable and faster to iterate with.

Common approaches include:

- **A general-purpose language bound to the engine** — such as C# in Unity, or C++ in Unreal, used to write scripts that hook directly into engine systems.
- **A dedicated scripting language** — such as GDScript in Godot, designed specifically to be simple and tightly integrated with that engine's node system.
- **Visual scripting** — such as Unreal's Blueprints, where logic is built by connecting nodes graphically instead of writing text-based code, lowering the barrier to entry for designers and artists.

Regardless of the language, scripts in an engine are typically attached to components (Lesson 5) and gain access to that object's transform, other components, and engine-provided functions for things like input, physics queries, and spawning objects.

| Engine | Scripting approach | Example syntax style |
|---|---|---|
| Unity | C# | `transform.position += Vector3.right * speed;` |
| Unreal | C++ or Blueprints | Visual node graph, or `AActor::Tick()` in C++ |
| Godot | GDScript | `position.x += speed * delta` |

---

## 13.2 Lifecycle Methods

Engines call specific, predictably-named functions on a script at specific moments — these are called **lifecycle methods**. Rather than a developer writing their own game loop from scratch (as shown conceptually in Lesson 2), the engine's runtime already contains that loop, and it calls into user scripts at the right points automatically. Common lifecycle methods include:

- **Start/Ready** — called once, when the object is first created or the scene loads; used for one-time setup.
- **Update** — called once per frame; used for logic that needs to run continuously, such as reading input or moving an object.
- **Fixed Update** — called at a fixed rate, independent of frame rate, commonly used for physics-related code (tying back to the fixed timestep discussed in Lesson 10).
- **On Destroy** — called just before an object is removed from the scene; used for cleanup.

A simplified pseudocode script illustrating lifecycle methods:

```
class PlayerMovement {
    function start() {
        speed = 5
    }

    function update(delta_time) {
        move_input = input.get_axis("Horizontal")
        transform.position.x += move_input * speed * delta_time
    }
}
```

---

## 13.3 Event Systems And Messaging

As a game grows, objects increasingly need to communicate without being tightly coupled to each other's internal details — for example, a `HealthBar` UI element needs to know when the player's health changes, but the `PlayerHealth` script shouldn't need to know anything about how the UI displays that information. Engines address this with **event systems** (sometimes called "signals," "delegates," or "messaging systems"), which let one piece of code **broadcast** that something happened, while any number of unrelated listeners can **subscribe** to react to it, without either side needing direct knowledge of the other.

A simplified pseudocode example:

```
// PlayerHealth script broadcasts an event, with no knowledge of who's listening:
function take_damage(amount) {
    current_health -= amount
    on_health_changed.broadcast(current_health)
}

// HealthBar script subscribes, with no knowledge of how damage happens:
function start() {
    player_health.on_health_changed.subscribe(update_health_bar_display)
}
```

This decoupling makes large projects far more maintainable — systems can be added, removed, or changed independently as long as the events they publish and subscribe to stay consistent.

---

## 13.4 State Machines

Much of gameplay logic boils down to an object being in one of several distinct **states**, with specific rules for how and when it transitions between them. A **state machine** is a pattern (and often a built-in engine tool) for organizing this cleanly, rather than relying on a tangle of boolean flags and conditional checks.

For example, an enemy character might have the states `Idle`, `Patrolling`, `Chasing`, and `Attacking`, with defined transition rules:

```
Idle -> Patrolling      (after a short wait)
Patrolling -> Chasing   (when the player is spotted)
Chasing -> Attacking    (when close enough to the player)
Chasing -> Patrolling   (if the player escapes)
```

Each state defines its own behavior for what the enemy does while in it (e.g., only in the `Attacking` state does the enemy deal damage), and the state machine handles moving between states based on the defined rules. This keeps behavior organized and predictable, and is a pattern you'll see again in Lesson 14 applied specifically to animation.

```
Idle:        [Enemy stands still, plays idle animation]
Patrolling:  [Enemy walks a fixed path between waypoints]
Chasing:     [Enemy moves directly toward the player's position]
Attacking:   [Enemy stops moving, deals damage on a cooldown timer]
```

Without a state machine, this same logic often devolves into a tangle of flags like `isChasing`, `isAttacking`, and `hasSeenPlayer`, all checked in nested `if` statements — a state machine keeps exactly one state active at a time, making the enemy's behavior far easier to reason about and debug.

---

[Previous](./[12]-Input-Handling.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[14]-Animation-Systems.md)
