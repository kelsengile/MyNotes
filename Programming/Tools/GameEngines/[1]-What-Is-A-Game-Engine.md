[Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[2]-Core-Engine-Architecture.md)

*Engine Foundations*

# Lesson 1 - What Is A Game Engine

## 1.1 Defining a Game Engine

A **game engine** is a piece of software that provides the core technology needed to build and run a game, so that developers don't have to build that technology from scratch for every project. At its heart, an engine is a collection of systems — rendering, physics, audio, input, scripting, and more — bundled together and exposed through a consistent set of tools and APIs.

Think of a game as a house. The engine is the foundation, plumbing, and electrical wiring: it's not the house itself, but without it you'd have to dig the foundation and run the wires yourself before you could even think about decorating a room. The "game" is what a developer builds using the engine's tools — the levels, characters, art, and rules that make the experience unique.

Most modern engines also ship with an **editor**: a visual application where developers place objects in a world, tweak properties, preview changes instantly, and assemble the final game without writing low-level rendering or math code by hand.

## 1.2 Engines vs Frameworks vs Libraries

These three terms get used loosely, but they describe different levels of structure:

- A **library** is a focused set of reusable code that you call into. For example, a physics library like Box2D gives you collision detection and rigid body simulation, but nothing else — you still write your own rendering, game loop, and input handling around it.
- A **framework** provides more structure than a library — it defines the overall shape of your program and calls *your* code at the right moments, rather than the other way around. A framework might dictate how your game loop is structured but still leave rendering or physics up to you or additional libraries.
- A **game engine** is a complete, integrated package: it typically bundles a renderer, a physics system, an audio system, an input system, a scripting environment, and often a visual editor, all designed to work together out of the box.

A useful way to remember the distinction: you call a library, a framework calls you, and an engine gives you an entire workshop with the framework, libraries, and tools already wired together.

## 1.3 Why Game Engines Exist

Before general-purpose engines were common, most game studios wrote their own technology for every game — often from scratch. This was extremely expensive: a team might spend a year building a renderer before writing a single level of actual gameplay.

Game engines exist to solve this by separating **reusable technology** from **game-specific content**. The same underlying rendering and physics code can power a racing game, a puzzle game, and a first-person shooter — only the assets, rules, and logic change. This separation lets:

- Small teams and solo developers ship games that would have required a large studio a decade earlier.
- Studios reuse and improve the same technology across multiple titles.
- Companies license their engines to other studios (Unity and Unreal both work this way), turning engine development into its own business.

## 1.4 Key Benefits

Using an existing engine instead of building one from scratch offers several concrete advantages:

- **Faster iteration** — an editor with a "play" button lets you test changes in seconds instead of rebuilding a whole program.
- **Cross-platform support** — engines abstract away the differences between PC, consoles, and mobile devices, so the same project can often be exported to many platforms with minimal changes.
- **Battle-tested systems** — physics, rendering, and audio are notoriously difficult to get right; using a mature engine means relying on code that thousands of other projects have already stress-tested.
- **Community and documentation** — popular engines have large communities, tutorials, and third-party asset stores, which lowers the learning curve significantly.
- **Focus on gameplay** — teams can spend their time on what makes their game unique instead of on low-level infrastructure.

The trade-off is flexibility: an engine makes assumptions about how games are structured, and working against those assumptions can be harder than writing custom code. This is why some large studios still maintain proprietary, in-house engines tailored exactly to their needs — a topic we'll return to in Lesson 3.

---

[Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[2]-Core-Engine-Architecture.md)
