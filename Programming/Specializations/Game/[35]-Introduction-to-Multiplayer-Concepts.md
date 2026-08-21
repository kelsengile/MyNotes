[Previous](./[34]-Particle-Effects-and-Visual-Feedback.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[36]-Client-Server-Models-for-Games.md)

*Multiplayer & Networking*

# Lesson 35 - Introduction to Multiplayer Concepts

## 35.1 Why Multiplayer Is Different

A single-player game only has to keep one machine's state consistent with itself. A multiplayer game has to keep *multiple* machines — each with its own hardware, network connection, and latency — agreeing on a shared version of reality, in something close to real time. This single constraint is the source of almost every unique challenge in multiplayer development: what happens when two players act at the same instant, what happens when a message arrives late, and what happens when a player's own client tries to cheat.

---

## 35.2 Common Multiplayer Models

- **Co-op** — players work together toward shared goals (e.g. a shared-world survival game).
- **Competitive (PvP)** — players compete against each other, often in symmetric matches (shooters, fighting games).
- **MMO (massively multiplayer online)** — large, persistent shared worlds with many players simultaneously, usually run on always-on servers.
- **Asynchronous multiplayer** — players don't need to be online at the same time (turn-based mobile games, "ghost" data in racing games).

Each model has very different networking requirements — an MMO needs a persistent backend, while a local co-op game might need almost no networking code at all.

---

## 35.3 Core Terminology

- **Client** — a player's copy of the game, running on their machine.
- **Server** — the authority that clients connect to; may be a player's machine (**host**) or a separate machine (**dedicated server**).
- **Peer** — in peer-to-peer setups, each client communicates directly with other clients rather than through a central server.
- **Latency (ping)** — the time it takes for data to travel from a client to the server and back.
- **Tick/tick rate** — how often the game simulation updates and network state is sent, measured in updates per second.

---

## 35.4 Challenges Unique to Multiplayer

- **Latency** — no connection is instant; every design decision has to account for the delay between an action and other players seeing it.
- **Synchronization** — all clients need to agree on the same game state, even though they're each simulating independently.
- **Cheating** — a client's local copy of the game can be modified by its player, so anything that matters for fairness generally can't be trusted to the client alone.
- **Scalability** — supporting more concurrent players multiplies bandwidth, server load, and the chance of edge cases occurring.

The following two lessons build on these ideas directly: Lesson 36 covers *who* is in charge of the game state, and Lesson 37 covers *how* that state actually gets kept in sync across the network.

[Previous](./[34]-Particle-Effects-and-Visual-Feedback.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[36]-Client-Server-Models-for-Games.md)
