[Previous](./[35]-Introduction-to-Multiplayer-Concepts.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[37]-Syncing-Game-State-Over-a-Network.md)

*Multiplayer & Networking*

# Lesson 36 - Client-Server Models for Games

## 36.1 Peer-to-Peer vs. Client-Server

- **Peer-to-peer (P2P)** — every client connects directly to every other client, with no central authority. Simple for very small player counts, but connections and state get harder to manage as more players join, and it's easy for any peer to cheat since no single machine is "in charge."
- **Client-server** — every client connects only to a central server, which owns the authoritative game state and relays updates to everyone. This is the dominant model for modern multiplayer games because it gives a single, trusted source of truth.

---

## 36.2 Dedicated Server vs. Listen Server

- **Dedicated server** — a separate machine (often in the cloud) runs the server with no player attached to it. More resilient (it doesn't disappear if a player disconnects) and harder to cheat against, but costs money to run and host.
- **Listen server** — one player's machine acts as both a client *and* the server (a "host"). Cheaper and simpler to set up, but the host has an inherent advantage (zero latency to their own actions) and the game ends for everyone if the host leaves.

---

## 36.3 Server Authority

In an **authoritative server** model, the server makes the final decision on everything that matters — did that shot hit, is that item now in the player's inventory, is that player dead. Clients send their *inputs* (movement, actions) rather than directly changing the shared state, and the server validates those inputs before applying them.

This matters because it closes off the most common cheating vector: a modified client can lie about what it *wants* to happen, but it can't force the server to accept an invalid outcome (like flying through a wall or dealing impossible damage) if the server checks it properly.

---

## 36.4 Matchmaking and Lobbies

Before a match even starts, most multiplayer games need:

- **Lobbies** — a waiting area where players gather, configure settings, and ready up before a match begins.
- **Matchmaking** — automatically grouping players into a match, often based on skill rating, region (to minimize latency), or party size.
- **Session/room management** — tracking which players belong to which active match, and handling players joining or leaving mid-session.

These systems typically run on separate backend services from the real-time game server itself, since matchmaking is a fundamentally different, less latency-sensitive problem than syncing live gameplay.

[Previous](./[35]-Introduction-to-Multiplayer-Concepts.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[37]-Syncing-Game-State-Over-a-Network.md)
