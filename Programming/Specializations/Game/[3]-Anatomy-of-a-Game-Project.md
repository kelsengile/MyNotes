[Previous](./[2]-Setting-Up-a-Game-Engine.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[4]-Prototyping-and-Game-Design-Documents.md)

*Getting Started*

# Lesson 3 - Anatomy of a Game Project

## 3.1 The Project Folder Structure

Every engine generates a project folder with a predictable layout. Understanding it prevents you from accidentally breaking things by moving or deleting the wrong folder.

- **Assets folder** (`Assets/` in Unity, `res://` in Godot, `Content/` in Unreal) — where all your art, audio, scripts, and scenes live.
- **Project settings files** — engine-specific configuration (e.g. `ProjectSettings/` in Unity, `project.godot` in Godot, `.uproject` in Unreal) that define build targets, input mappings, and physics defaults.
- **Library/Cache/Intermediate folders** — auto-generated, regenerable data the engine uses internally. These are safe to delete (the engine rebuilds them) and should **never** be checked into version control.
- **Build/Output folders** — where exported, playable builds of your game are placed.

---

## 3.2 Assets, Scenes, and Scripts

- **Assets** are the raw building blocks: images, 3D models, audio clips, fonts.
- **Scenes** (also called Levels or Maps) are containers that arrange GameObjects/Entities/Nodes in space — a menu, a level, a boss arena are all typically separate scenes.
- **Scripts** are the code files that define behavior and get attached to objects within a scene.

A useful mental model: assets are *ingredients*, scripts are *recipes*, and scenes are the *finished dishes* that combine both.

---

## 3.3 Build Settings and Configuration Files

Every engine has a central place where you define:

- Which scenes are included in the final build, and in what order.
- Which platforms you're targeting (PC, mobile, console, web).
- Quality and rendering settings per platform.
- Application metadata (name, icon, version number).

Getting familiar with this screen early avoids surprises later when you try to export your first build (covered in Lesson 42).

---

## 3.4 Common Conventions Across Engines

Regardless of which engine you use, a few organizational habits pay off:

- Group assets by **type or feature**, not by chronology (e.g. `Assets/Characters/Player/` rather than `Assets/Stuff/NewFolder2/`).
- Keep a consistent naming scheme (e.g. `PascalCase` for scripts, `snake_case` for asset files) — pick one and stick to it.
- Separate **third-party assets** (plugins, purchased packs) from your own work, so updates don't overwrite your changes.
- Keep scenes small and focused; a single giant "everything" scene becomes unmanageable and slow to load.

[Previous](./[2]-Setting-Up-a-Game-Engine.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[4]-Prototyping-and-Game-Design-Documents.md)
