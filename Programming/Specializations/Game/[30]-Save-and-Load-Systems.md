[Previous](./[29]-Dialogue-and-Quest-Systems.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[31]-Procedural-Generation.md)

*Game Systems & Logic*

# Lesson 30 - Save & Load Systems

## 30.1 What Needs to Be Saved?

Not everything in a game's runtime state belongs in a save file. A useful filter: **would the player expect this to persist if they quit and reopened the game?** Typically this includes:

- Player progress (level, position, unlocked areas).
- Inventory and equipment.
- Quest states and story flags.
- Settings (though these are often saved separately from progress, so they persist even across different save slots).

Purely transient state — like the exact position of a bullet mid-flight — should generally *not* be saved.

---

## 30.2 Serialization Formats

**Serialization** is the process of converting in-memory game data (objects, variables) into a format that can be written to disk and later reconstructed. Common formats:

- **JSON** — human-readable, easy to debug, widely supported.
- **Binary** — more compact and faster to read/write, but not human-readable, making it harder to debug or hand-edit.
- **Engine-specific formats** — some engines provide their own built-in serialization (e.g. Unity's `PlayerPrefs` for simple key-value data, or custom `ISerializable` implementations for complex objects).

A common approach: define a plain "save data" class containing only the fields you actually want saved, populate it from your live game objects, then serialize *that* — rather than trying to directly serialize complex gameplay objects themselves.

---

## 30.3 Save Files and Slots

Most games support multiple **save slots**, letting players maintain several separate playthroughs. Considerations:

- **File naming/location** — using the platform's designated save-data directory rather than an arbitrary location, both for good practice and because some platforms restrict where games are allowed to write files.
- **Autosave vs. manual save** — autosaving at key points (level completion, checkpoints) reduces lost progress, but shouldn't fully replace manual saves for player-driven control.
- **Save versioning** — including a version number in the save format so future updates can detect and migrate older save files rather than breaking them outright.

---

## 30.4 Common Pitfalls

- **Saving object references directly** — in-memory references (pointers to specific objects) aren't meaningful once the game restarts; save IDs or structured data instead, and reconstruct references on load.
- **Not handling corrupted/missing save files gracefully** — always wrap load logic in error handling so a corrupted save doesn't crash the game outright.
- **Forgetting to test loading on a fresh install** — a save/load system that only works because leftover data exists in memory from testing will fail for real players starting from scratch.

[Previous](./[29]-Dialogue-and-Quest-Systems.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[31]-Procedural-Generation.md)
