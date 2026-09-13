[Previous](./[0]-Introduction-to-Godot.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[2]-The-Godot-Editor-Interface.md)

*Getting Started*

# Lesson 1 - Installing And Setting Up Godot

## 1.1 Downloading Godot (Standard vs .NET Builds)

Godot ships as a single, small executable — there's no installer, no background service, and no account required. You download it from [godotengine.org/download](https://godotengine.org/download/) and run it directly.

When you download Godot, you'll notice two build options:

| Build | Scripting Languages | Best For |
|---|---|---|
| **Standard** | GDScript, VisualScript, C++ (via GDExtension) | Most beginners, GDScript-only projects |
| **.NET (Mono)** | GDScript **and** C# | Developers who want to write gameplay code in C# |

If you're new to game development, start with the **Standard** build. It's smaller, starts faster, and every tutorial in this Topic assumes you're using it. You can always switch to the .NET build later — projects aren't locked to one build type, though mixing GDScript and C# in the same project does require the .NET build.

Godot is also available for Windows, macOS, and Linux, and the editor itself is portable — on Windows and Linux you can just unzip it and run the `.exe` or binary with no installation step at all.

---

## 1.2 First Launch And The Project Manager

The first thing you'll see when you open Godot is the **Project Manager** — not the editor itself. Think of it as a launcher that lists every Godot project on your machine:

```
┌─────────────────────────────────────────┐
│  Godot Project Manager                   │
├─────────────────────────────────────────┤
│  [+ New]  [Import]  [Scan]               │
│                                           │
│  ▸ MyPlatformer         (Godot 4.3)      │
│  ▸ SpaceShooterDemo     (Godot 4.3)      │
│  ▸ TopDownRPG           (Godot 4.2)      │
└─────────────────────────────────────────┘
```

From here you can create a new project, import an existing one from disk, or open something you've worked on before. Each project is completely self-contained in its own folder — Godot doesn't install anything globally, so you can have as many projects as you like without them interfering with each other.

---

## 1.3 Creating A New Project

Clicking **New** asks you for three things:

1. **Project Name** — used for the window title and default export name.
2. **Project Path** — an empty folder where Godot will create its project files.
3. **Renderer** — Godot 4.x offers a choice here:
   - **Forward+** — best visual quality, designed for desktop GPUs.
   - **Mobile** — lighter-weight, targets phones and lower-end hardware.
   - **Compatibility** — runs on older GPUs and web exports, using OpenGL instead of Vulkan.

For a first project, **Forward+** on desktop or **Mobile** if you're targeting phones are both safe defaults — you can change the renderer later in Project Settings if needed.

Once created, Godot generates a `project.godot` file in that folder. This single text file is what makes a folder a "Godot project" — it stores your project's settings, and Godot detects it automatically the next time you scan that folder.

---

## 1.4 Godot's Version History And Release Channels

Godot has two actively maintained major versions you'll see referenced online:

- **Godot 3.x** — the previous stable series, still used in many existing tutorials and older projects.
- **Godot 4.x** — the current series, with a rewritten renderer, improved physics, and a redesigned UI system. This Topic teaches **Godot 4.x**.

Within Godot 4, releases follow a pattern like `4.3`, `4.3.1`, `4.4`, etc. — the first number is the major version, the second is a feature release, and a third number (if present) is a bugfix patch. Feature releases (4.2 → 4.3) can introduce new nodes or change APIs slightly, so if a tutorial behaves differently than what you see, checking which version it was written for is a good first troubleshooting step.

Godot also has a **stable** channel (what you should use for real projects) and pre-release channels (`dev`, `beta`, `RC`) where new features are tested before becoming stable. Unless you specifically want to try an unreleased feature, always download from the **Stable Releases** section of the download page.

> **Tip:** The Project Manager will offer to convert a project between versions if you open an older project with a newer editor. Always back up (or commit to version control) before accepting a version conversion — it can rewrite files in ways that are hard to undo.

[Previous](./[0]-Introduction-to-Godot.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[2]-The-Godot-Editor-Interface.md)
