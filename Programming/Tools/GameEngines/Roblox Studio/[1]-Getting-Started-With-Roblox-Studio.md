[Previous](./[0]-Introduction-to-RobloxStudio.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[2]-The-Roblox-Studio-Interface.md)

*Getting Started*

# Lesson 1 - Getting Started With Roblox Studio

## 1.1 Creating A Roblox Account And Installing Studio

Roblox Studio requires a Roblox account — the same kind used to play experiences on the platform. If you don't already have one, create it at [roblox.com](https://www.roblox.com), then install Studio from [create.roblox.com/docs/studio/setup](https://create.roblox.com/docs/studio/setup). Studio is available for Windows and macOS.

Unlike a general-purpose engine, Studio is tied directly to your Roblox account — when you open it, you sign in with that same account, and everything you build (your **places**) is saved to Roblox's cloud by default rather than as loose files on disk, though you can also save `.rbxl` files locally if you prefer.

---

## 1.2 Templates And Starting A New Place

When you launch Studio, you're shown a template picker rather than a blank editor:

```
┌───────────────────────────────────────┐
│  New                                    │
├───────────────────────────────────────┤
│  [Baseplate]  [Flat Terrain]  [Obby]    │
│  [Racing]     [Tycoon]        [Village] │
└───────────────────────────────────────┘
```

- **Baseplate** — a single flat gray block and nothing else. The standard starting point for learning, since it gives you a floor to stand on with no assumptions about what you're building.
- Other templates (Obby, Racing, Tycoon, etc.) come pre-populated with genre-specific scripts and building blocks, useful for seeing a complete example but not ideal for a first lesson since there's a lot already there to understand.

For this Topic, start from **Baseplate** — it mirrors starting from an empty scene in a traditional engine, with nothing hidden from you.

A single Roblox project is called a **place**, saved as a `.rbxl` file if stored locally, or synced directly to your Roblox account's cloud storage — most creators work with cloud saving, which also enables Roblox's built-in version history.

---

## 1.3 Roblox Terminology (Experience, Place, Game)

Roblox has specific vocabulary worth learning early, since it doesn't map one-to-one onto terms from other engines:

| Term | Meaning |
|---|---|
| **Experience** | The overall product a player finds and joins on Roblox — roughly equivalent to "a game" in the traditional sense. |
| **Place** | A single 3D environment/level within an experience, edited as one file in Studio. A simple experience might have just one place; a larger one might have several (e.g. a lobby place and a separate gameplay place), linked together. |
| **Game** | Used loosely, and often interchangeably with "Experience" in casual conversation — but technically, the underlying data structure Roblox uses to group a set of places together is called a `DataModel`, and "Game" isn't a formal API term the way Experience and Place are. |
| **Instance** | The base building block of everything inside a place — parts, scripts, sounds, lights. Covered fully in Lesson 3. |

The distinction that matters most for beginners: **one Experience can contain multiple Places**, but when you're just getting started, you'll typically work with a single place, which is functionally "the whole game."

---

## 1.4 Studio vs The Roblox Player App

It's worth understanding the split between the two applications you'll use:

- **Roblox Studio** — the editor. This is where you build, script, and test. Only creators use Studio.
- **Roblox Player** (the regular Roblox app) — what players use to actually join and play published experiences. It has no editing capabilities.

When you click **Play** inside Studio, it doesn't open the Player app — it runs a live simulation *inside* Studio itself, complete with a simulated server and client (more on this distinction in Lesson 2.4 and again in Lesson 5). This means you can iterate and test almost instantly without ever leaving the editor, and you only need the separate Player app to see how your experience behaves for real players after publishing.

> **Tip:** Because Studio simulates the client-server model even during local testing, bugs that only show up in a live multi-player session can sometimes be caught early by testing with Studio's multi-client test mode (Lesson 2.4) before ever publishing.

[Previous](./[0]-Introduction-to-RobloxStudio.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[2]-The-Roblox-Studio-Interface.md)
