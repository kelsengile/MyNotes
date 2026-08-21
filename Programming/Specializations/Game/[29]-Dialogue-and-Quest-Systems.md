[Previous](./[28]-Inventory-and-Item-Systems.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[30]-Save-and-Load-Systems.md)

*Game Systems & Logic*

# Lesson 29 - Dialogue & Quest Systems

## 29.1 Dialogue Trees

A **dialogue tree** structures a conversation as a series of connected nodes, where each node is a line of dialogue and its possible player responses:

```
NPC: "Have you seen the missing shipment?"
 ├── "No, tell me more." → NPC explains the shipment
 └── "Not my problem." → conversation ends, NPC remembers the response
```

Branches can lead to different outcomes, unlock new quest states, or simply loop back to earlier points, and are often authored in a visual node-based editor rather than hardcoded, so writers can iterate without programmer involvement.

---

## 29.2 Quest States and Objectives

A quest is typically modeled as a small state machine of its own:

- **Not Started** — the quest hasn't been offered/discovered yet.
- **Active** — the player has accepted it and is working through its objectives.
- **Completed** — all objectives are met, ready to turn in (or auto-completed).
- **Failed** — the quest can no longer be completed (a time limit expired, an NPC died).

An active quest usually tracks one or more **objectives** (e.g. "Collect 5 herbs", "Talk to the Elder"), each independently trackable so the UI can show partial progress.

---

## 29.3 Triggers and Conditions

Quests and dialogue rarely progress on their own — they need to be **triggered** by gameplay events:

- Entering a specific area (a trigger collider, Lesson 16).
- Defeating a specific enemy.
- Picking up a specific item.
- Reaching a certain time or story flag.

A clean design routes all of these through a shared event system — a `QuestManager` that listens for gameplay events and checks them against active quest objectives — rather than scattering quest-checking logic throughout unrelated gameplay code.

---

## 29.4 Data-Driven Dialogue

For anything beyond a handful of simple conversations, hardcoding dialogue directly in code becomes unmanageable. Most shipped games instead store dialogue and quest data in external files or structured data assets (JSON, YAML, or engine-specific data objects), which:

- Lets writers edit content without recompiling code.
- Makes localization (Lesson 46) far more practical, since translated text can simply replace the source-language file.
- Keeps quest/dialogue logic code generic — reading "which node comes next" from data, rather than encoding each specific conversation as its own function.

[Previous](./[28]-Inventory-and-Item-Systems.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[30]-Save-and-Load-Systems.md)
