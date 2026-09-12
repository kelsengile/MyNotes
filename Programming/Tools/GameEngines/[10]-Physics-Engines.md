[Previous](./[9]-2D-Vs-3D-Rendering.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[11]-Collision-Detection-And-Response.md)

*Physics And Interaction*

# Lesson 10 - Physics Engines

## 10.1 What Is a Physics Engine

A **physics engine** is the subsystem responsible for simulating physical behavior in a game world — gravity, momentum, collisions, and how objects push, bounce off, or come to rest against each other. Rather than a developer manually scripting "if the ball hits the wall, make it bounce at this angle," a physics engine calculates these interactions automatically based on general physical rules, applied consistently to every object that opts into the simulation.

Physics engines typically run on a **fixed timestep**, separate from the variable rendering rate discussed in Lesson 7. This keeps the simulation stable and deterministic — running physics at a consistent rate (for example, exactly 50 times per second) regardless of how fast or slow frames are rendering prevents objects from behaving inconsistently or "tunneling" through walls at low frame rates.

---

## 10.2 Rigidbodies And Colliders

Two components form the foundation of most physics simulations:

- A **rigidbody** marks an object as being subject to physics simulation — it has properties like mass, drag, and velocity, and the physics engine will move it according to forces applied to it (including gravity). An object *without* a rigidbody is typically treated as **static** — it can be collided with, but it never moves on its own (like a floor or wall).
- A **collider** defines an object's physical shape for the purposes of detecting collisions — this is often a simplified approximation of the object's visual shape (a box or sphere collider around a detailed character model, for example), since simple shapes are far cheaper to calculate collisions for than exact, detailed geometry.

An object can have a collider without a rigidbody (a static wall), and in some engines, a rigidbody without a visible mesh at all (an invisible physical barrier). The two components are related but conceptually distinct: the rigidbody governs *how an object moves*, while the collider governs *what shape it collides as*.

| Object | Rigidbody? | Collider? | Behavior |
|---|---|---|---|
| Floor | No | Yes | Static — never moves, but blocks other objects |
| Bouncing ball | Yes | Yes | Falls under gravity, bounces off surfaces |
| Invisible wall | Yes (kinematic) or No | Yes | Blocks movement, never visibly renders |
| Background decoration | No | No | Purely visual, ignored by physics entirely |

---

## 10.3 Forces And Gravity

Physics engines simulate motion by applying **forces** to rigidbodies. A force, in simplified terms, is a push or pull that changes an object's velocity over time, following the classic physics relationship:

```
force = mass × acceleration
```

**Gravity** is simply a constant downward force applied automatically to every rigidbody (unless explicitly disabled for a specific object, such as a flying enemy). Beyond gravity, engines expose functions for scripts to apply their own forces — for example, applying an upward force to make a character jump, or an outward force to simulate an explosion pushing nearby objects away.

A simplified pseudocode example of applying a jump force:

```
function on_jump_pressed() {
    rigidbody.apply_force(direction: UP, strength: jump_force)
}
```

Because the physics engine handles the resulting motion (acceleration, deceleration due to gravity, eventual landing) automatically once a force is applied, developers rarely need to manually calculate trajectories by hand.

---

## 10.4 Physics Materials

Just as a visual material (Lesson 8) defines how a surface looks, a **physics material** defines how a surface behaves during physical interactions — specifically its **friction** (resistance to sliding) and **restitution/bounciness** (how much energy is retained when it collides with something, i.e., how "bouncy" it is).

A rubber ball and a block of ice might share the exact same collider shape and rigidbody settings, but behave completely differently in a physics simulation simply because they're assigned different physics materials:

- The rubber ball might have **high friction** (it doesn't slide easily) and **high bounciness** (it rebounds strongly off surfaces).
- The ice block might have **low friction** (it slides easily) and **low bounciness** (it doesn't rebound much at all).

Physics materials let developers reuse the same underlying physics simulation code across very different-feeling objects, purely by adjusting a small set of numeric properties rather than writing custom collision-response logic for every object type.

---

[Previous](./[9]-2D-Vs-3D-Rendering.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[11]-Collision-Detection-And-Response.md)
