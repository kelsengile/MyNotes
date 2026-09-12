[Previous](./[2]-Core-Engine-Architecture.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[4]-Scenes-And-The-Game-World.md)

*Engine Foundations*

# Lesson 3 - Popular Game Engines

This lesson stays tool-agnostic in spirit, but it's useful to know the landscape of engines you're likely to encounter before diving into fundamentals. Each engine implements the same core concepts covered in this Topic in its own way.

## 3.1 Unity

Unity is a widely-used, general-purpose engine known for its gentle learning curve, huge asset store, and strong support for both 2D and 3D games. It uses **C#** as its primary scripting language and follows a **GameObject + Component** architecture, where behavior is built by attaching small, reusable scripts and components to objects in a scene.

Unity is especially popular for:

- Indie and small-team projects, thanks to its approachable editor and large community.
- Mobile games, due to strong cross-platform export support.
- Prototyping, because of how quickly a playable scene can be assembled.

Notable titles built in Unity include *Hollow Knight*, *Cuphead*, and *Among Us* — a good illustration of how far the same general-purpose engine can stretch across very different art styles and genres.

---

## 3.2 Unreal Engine

Unreal Engine, developed by Epic Games, is known for its high-fidelity, high-performance rendering and is widely used in AAA game production, as well as film and architectural visualization. It uses **C++** for performance-critical code, alongside a visual scripting system called **Blueprints** that lets designers build logic without writing traditional code.

Unreal is especially popular for:

- Large-scale, graphically demanding 3D games.
- Teams that want a mix of visual scripting (Blueprints) and traditional programming (C++).
- Projects that benefit from Unreal's advanced built-in rendering features, such as dynamic global illumination.

Unreal powers games like *Fortnite* and *Gears of War*, and its rendering tech (particularly the more recent Nanite and Lumen systems) has also seen adoption outside games entirely, in film previsualization and virtual production.

---

## 3.3 Godot

Godot is a free, open-source engine that has grown quickly in popularity, particularly for 2D games, though it supports 3D as well. It uses its own scripting language, **GDScript** (which is intentionally similar to Python), along with support for C# and other languages. Godot's architecture is built around a flexible **node** system, where every element of a game — a sprite, a sound, a piece of UI — is a node that can be composed into trees.

Godot is especially popular for:

- Developers who want a completely free and open-source toolchain with no licensing fees.
- 2D-focused games, where its tools are considered especially strong.
- Smaller executable sizes and a lightweight editor compared to some competitors.

*Celeste*-style precision platformers and games like *Brotato* have been built with Godot, and its adoption grew noticeably after a wave of developers looked for alternatives following licensing controversies at other engines.

| Engine | Primary Language | Architecture Style | License |
|---|---|---|---|
| Unity | C# | GameObject + Component | Free tier + revenue-based plans |
| Unreal | C++ / Blueprints | Actor + Component | Free, royalty above a revenue threshold |
| Godot | GDScript (+ C#) | Node tree | Free and open-source (MIT) |

---

## 3.4 Custom and Proprietary Engines

Not every game is built on a general-purpose, publicly available engine. Large studios often build **custom (proprietary) engines** tailored precisely to the kind of games they make. Well-known examples include id Tech (used for games like *DOOM*), and various in-house engines built by major publishers for specific franchises.

Custom engines make sense when:

- A studio has very specific performance or rendering needs that a general-purpose engine doesn't serve well.
- The studio produces many games in the same genre, so the upfront investment in a specialized engine pays off across multiple titles.
- Licensing costs or terms for existing engines are undesirable at a studio's scale.

The trade-off is significant: building and maintaining a custom engine requires a large, dedicated engineering team, which is usually only realistic for well-funded studios. For the vast majority of developers, a general-purpose engine like Unity, Unreal, or Godot offers a far better return on the time invested.

Other well-known proprietary engines include Naughty Dog's engine (used for *The Last of Us* series), CD Projekt Red's REDengine (used for *The Witcher 3*, later replaced by Unreal for future titles), and Rockstar's RAGE (used across the *Grand Theft Auto* and *Red Dead Redemption* series).

---

[Previous](./[2]-Core-Engine-Architecture.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[4]-Scenes-And-The-Game-World.md)
