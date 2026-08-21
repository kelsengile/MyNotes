[Previous](./[33]-UI-UX-for-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[35]-Introduction-to-Multiplayer-Concepts.md)

*Audio & UX*

# Lesson 34 - Particle Effects & Visual Feedback

## 34.1 What Are Particle Systems?

A **particle system** spawns and manages large numbers of small, simple visual elements ("particles") to create effects that would be impractical to animate by hand — smoke, fire, sparks, magic spells, rain. Each particle is typically just a small sprite or simple shape, but combined in large numbers and animated with randomized variation, they read as complex, organic effects.

---

## 34.2 Particle Properties

Particle systems are typically configured through a set of common properties:

- **Emission rate** — how many particles spawn per second.
- **Lifetime** — how long each particle exists before disappearing.
- **Velocity/direction** — how particles move after spawning, often with randomized variation for a more natural look.
- **Size and color over lifetime** — particles often shrink, fade, or change color as they age (e.g. fire particles starting bright yellow and fading to dark red/transparent).
- **Shape/emission volume** — the region particles spawn from (a point, a cone, a sphere), shaping the overall look of the effect.

---

## 34.3 Common Effects

- **Explosions** — a short, high-emission-rate burst rather than continuous emission.
- **Smoke/fire** — continuous emission with particles that rise, expand, and fade over their lifetime.
- **Impact effects** — a small, quick burst of particles (sparks, dust, debris) triggered exactly at the moment of a collision or hit, reinforcing the sense of impact.
- **Ambient effects** — falling leaves, floating dust motes, drifting embers — subtle, continuous effects that add life to an environment without demanding attention.

---

## 34.4 Feedback and Game Feel

Particle effects are one part of a broader concept called **game feel** or **juice** — the layered small details that make actions feel satisfying and impactful, even when they don't change the actual outcome of gameplay. Related techniques often combined with particles:

- **Screen shake** — a brief camera jolt on impactful events (explosions, big hits), used sparingly so it doesn't become disorienting or overused.
- **Hit-stop/freeze frames** — briefly pausing the game for a few frames at the moment of an impactful hit, emphasizing its weight.
- **Squash and stretch** — slightly deforming an object's scale on impact or landing, a classic animation technique that reads as more dynamic and alive than a rigid, unchanging shape.

None of these individually make a game "fun," but together they're often what separates a mechanically identical game that feels flat from one that feels satisfying to play.

[Previous](./[33]-UI-UX-for-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[35]-Introduction-to-Multiplayer-Concepts.md)
