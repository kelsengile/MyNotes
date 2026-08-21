[Previous](./[36]-Client-Server-Models-for-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[38]-Version-Control-for-Game-Projects.md)

*Multiplayer & Networking*

# Lesson 37 - Syncing Game State Over a Network

## 37.1 Network Ticks and Snapshots

Instead of sending updates the instant something changes, most multiplayer games run the network on a fixed **tick rate** (e.g. 20, 30, or 60 times per second). On each tick, the server gathers a **snapshot** of the relevant game state (positions, health, etc.) and sends it to clients. Lower tick rates use less bandwidth but feel less responsive; higher tick rates feel smoother but cost more server resources.

---

## 37.2 State Sync vs. Input Sync

- **State synchronization** — the server sends the *result* of the simulation (e.g. "this player is now at this position") directly to clients.
- **Input synchronization (deterministic lockstep)** — the server sends *inputs* (e.g. "this player pressed forward"), and every client simulates the outcome identically and independently. This uses far less bandwidth but requires every client's simulation to be perfectly deterministic, since any small divergence compounds over time.

Most modern action games use state sync for critical values and combine it with input-based prediction (below) to stay responsive.

---

## 37.3 Client-Side Prediction and Reconciliation

Waiting for a round trip to the server before showing the result of a player's own input would feel laggy, so clients typically:

1. **Predict** — apply the player's input locally and show the result immediately, without waiting for the server.
2. **Reconcile** — when the server's authoritative snapshot arrives, compare it to the predicted state. If they match, nothing visible happens. If they differ, the client corrects itself, ideally smoothly rather than as a visible snap.

This keeps a player's *own* actions feeling instant, while still letting the server have the final, authoritative say.

---

## 37.4 Interpolation and Lag Compensation

- **Interpolation** — for *other* players' movement, clients smoothly blend between the last two received snapshots rather than snapping to each new position, hiding the gaps between network updates.
- **Lag compensation** — on the server, accounting for the fact that by the time a shot arrives, the target has likely already moved. A common technique is to briefly "rewind" other players to where they appeared on the shooter's screen at the moment they fired, so a shot that looked accurate to the shooter is judged fairly.

Together, prediction, interpolation, and lag compensation are what make an inherently laggy network connection *feel* responsive and fair to every player.

[Previous](./[36]-Client-Server-Models-for-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[38]-Version-Control-for-Game-Projects.md)
