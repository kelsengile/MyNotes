[Previous](./[25]-AI-and-NPC-Behavior-Basics.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[27]-Game-State-Management.md)

*Gameplay Systems*

# Lesson 26 - Pathfinding

## 26.1 What Is Pathfinding?

Pathfinding is the process of calculating a route for an NPC to travel from one point to another while avoiding obstacles. Without it, NPCs can only move in straight lines and will get stuck against walls or fail to navigate around obstacles — pathfinding is what allows an enemy to intelligently walk around a wall to reach the player on the other side.

---

## 26.2 Navigation Meshes (NavMesh)

A **navigation mesh (NavMesh)** is a simplified representation of which areas of a level are walkable, generated automatically from level geometry:

1. The engine analyzes your level's static colliders/geometry.
2. It generates a mesh covering only the walkable surfaces, excluding walls, steep slopes, and obstacles.
3. AI agents then find paths across this simplified mesh instead of the full, detailed level geometry, which is far cheaper to compute.

Most engines provide built-in NavMesh generation and agent components, making this the most common approach to pathfinding in shipped games.

---

## 26.3 The A* Algorithm (Conceptually)

**A\*** (pronounced "A-star") is the classic algorithm underlying most pathfinding systems, including most NavMesh implementations. Conceptually, it:

1. Breaks the walkable area into nodes (either a grid or the NavMesh's internal triangulation).
2. Explores paths outward from the start node, always prioritizing the path that looks most promising — combining "distance already traveled" with an estimate of "distance remaining to the goal."
3. Stops once it reaches the destination node, having found a short (though not always mathematically shortest) path efficiently, without exhaustively checking every possible route.

You rarely need to implement A* from scratch for typical NPC pathfinding, since engines provide it built-in, but understanding the concept helps when tuning pathfinding performance or debugging odd NPC routes.

---

## 26.4 Dynamic Obstacles

Static level geometry is only part of the picture — games also need to handle obstacles that move or appear at runtime (a closing door, other NPCs, destructible cover):

- **NavMesh obstacles** — components that carve a temporary hole in the NavMesh while active, so agents route around them.
- **Local avoidance** — a separate, lighter-weight system that adjusts an agent's immediate movement to avoid other nearby moving agents, preventing NPCs from awkwardly overlapping each other while still following their overall path.

Combining a baked NavMesh for large-scale routing with local avoidance for moment-to-moment movement is the standard approach used by most modern games with pathfinding NPCs.

[Previous](./[25]-AI-and-NPC-Behavior-Basics.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[27]-Game-State-Management.md)
