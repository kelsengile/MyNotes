[Previous](./[0]-Introduction-to-UnrealEngine.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[2]-The-Unreal-Editor-Interface.md)

*Getting Started*

# Lesson 1 - Installing Unreal Engine And Epic Games Launcher

## 1.1 Epic Games Launcher And Account Setup

Unreal Engine is installed and managed through the **Epic Games Launcher**, similar in role to Unity Hub from the Unity Topic — it's a separate application you install first, which then handles downloading Engine versions, managing your projects, and accessing the free Marketplace assets tied to your Epic Games account.

```
┌───────────────────────────────────────┐
│  Epic Games Launcher                    │
├───────────────────────────────────────┤
│  Library | Unreal Engine | Marketplace  │
├───────────────────────────────────────┤
│  Engine Versions:                        │
│   ▸ 5.4.4                                 │
│   ▸ 5.3.2                                 │
│  [+ Install Engine]                       │
└───────────────────────────────────────┘
```

Download the Launcher from [unrealengine.com/download](https://www.unrealengine.com/download), sign in with a free Epic Games account, and use the **Unreal Engine** tab's **Library** section to install your first Engine version.

---

## 1.2 Engine Versions And Installing Unreal

Like Unity, Unreal supports installing **multiple Engine versions side by side**, and a project is tied to whichever major.minor version it was created in (e.g. `5.4`). Opening a project with a newer Engine version will prompt a conversion, similar to the version-conversion prompt covered in the Godot Topic — always back up or commit to version control first.

A full Unreal Engine installation is significantly larger than Godot or even Unity's editor download — often 30+ GB once all optional components (like platform-specific build tools) are included — so it's worth having sufficient disk space and a stable connection before starting an install.

Unreal's version numbers follow a `Major.Minor.Patch` pattern (e.g. `5.4.4`), with major version 5 being the current generation (Unreal Engine 4 is the previous, still-used-in-places generation, but this Topic covers **Unreal Engine 5**). Feature-complete point releases (`5.3` → `5.4`) can introduce new systems or workflow changes, so if a tutorial behaves differently than expected, checking which version it targets is a reasonable first troubleshooting step — the same advice given for Godot's version releases.

---

## 1.3 Creating A New Project And Templates

From the Launcher's **Unreal Engine > Library** tab, **Launch** opens the **Project Browser**, where you choose a category and template before creating a new project:

| Category | Example Templates |
|---|---|
| **Games** | Blank, Third Person, First Person, Top Down, Vehicle |
| **Film/Video & Live Events** | Virtual production and cinematic-focused starting points |
| **Architecture** | Optimized for archviz walkthroughs |
| **Automotive, Product Design, Manufacturing** | Industrial visualization templates |

Within a Games template, you also choose:

- **Blueprint** or **C++** — whether the template's starter logic is provided as Blueprints or as C++ classes (you can always add the other later; this just determines what the starter content uses).
- **Target Platform** — Desktop or Mobile, affecting some default quality settings.
- **Quality Preset** — Maximum Quality or Scalable, affecting default rendering settings (Lumen, Nanite) covered further in the Building Features lessons.

For a first project, the **Blank** or **Third Person** template using **Blueprint** is a reasonable, approachable starting point — Blank gives you an empty level to build from scratch, while Third Person gives you a pre-built character and camera to explore and learn from.

---

## 1.4 Unreal's Licensing Model

Unreal Engine is **free to download and use**, including full source code access, with a distinct royalty-based model rather than an upfront subscription:

- **No cost to develop** — you can build and release a project without paying anything upfront.
- **Royalty on revenue** — once a shipped product's lifetime gross revenue crosses a defined threshold (currently published on Epic's official licensing page, since exact figures are updated periodically), a royalty percentage applies to revenue beyond that threshold.
- **Exceptions** — certain distribution channels (e.g. releasing through the Epic Games Store) and custom licensing arrangements can change these terms; the authoritative, current terms are always on Epic's official Unreal Engine EULA page rather than any third-party summary.

For learning, prototyping, and most hobby projects, this means there's effectively no cost at all — the royalty model only becomes relevant once a project is actually earning significant revenue, at which point checking Epic's current official terms directly is the right move rather than relying on older or secondhand figures.

[Previous](./[0]-Introduction-to-UnrealEngine.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[2]-The-Unreal-Editor-Interface.md)