[Previous](./[24]-Character-Movement-and-Controllers.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[26]-Pathfinding.md)

*Gameplay Systems*

# Lesson 25 - AI & NPC Behavior Basics

## 25.1 What Is Game AI?

"AI" in games rarely means machine learning — it almost always refers to **scripted decision-making** that makes non-player characters (NPCs) appear to act intelligently: patrolling, chasing, attacking, fleeing, or reacting to the player. The goal is usually to create *believable, fun-to-play-against* behavior, not realistic intelligence.

---

## 25.2 Finite State Machines

A **Finite State Machine (FSM)** is one of the most common and approachable ways to structure NPC behavior. An NPC exists in exactly one **state** at a time, and transitions between states based on conditions:

```
Patrol → (sees player) → Chase → (loses sight / out of range) → Search → (gives up) → Patrol
Chase → (close enough) → Attack → (player out of range) → Chase
```

Each state defines its own update logic (what the NPC does while in that state) and its own transition conditions (when to switch to a different state). This keeps behavior organized and easy to debug, since you can always ask "what state is this NPC in right now?"

---

## 25.3 Behavior Trees (Overview)

For more complex AI, **behavior trees** offer a more flexible, hierarchical alternative to FSMs. A behavior tree is built from nodes like:

- **Selector** — tries each child in order until one succeeds (like a fallback list of options).
- **Sequence** — runs each child in order, only succeeding if all of them do (like a checklist).
- **Leaf/action nodes** — the actual behaviors (MoveTo, Attack, PlaySound).

Behavior trees scale better than FSMs for NPCs with many possible behaviors, since new branches can be added without rewriting the entire state graph, but they're more complex to set up initially — FSMs are usually the better starting point for simpler enemies.

---

## 25.4 Simple Decision Making

Beyond structure, NPCs need ways to sense and evaluate the world:

- **Vision/line-of-sight checks** — using raycasts (Lesson 20) to determine if a clear line exists between the NPC and the player, often combined with a field-of-view angle check.
- **Detection ranges** — simple distance checks to decide when an NPC notices the player.
- **Weighted decisions/utility scoring** — assigning a score to multiple possible actions and picking the highest-scoring one, useful when several actions could all be valid at once (e.g. choosing between attacking, retreating, or calling for backup based on current health and distance).

[Previous](./[24]-Character-Movement-and-Controllers.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[26]-Pathfinding.md)
