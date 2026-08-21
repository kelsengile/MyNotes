[Previous](./[38]-Version-Control-for-Game-Projects.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[40]-Performance-Optimization-for-Games.md)

*Tooling & Production*

# Lesson 39 - Asset Pipelines & Import Workflows

## 39.1 What Is an Asset Pipeline?

An **asset pipeline** is the path a piece of content takes from being created in an external tool (Photoshop, Blender, a DAW) to being usable inside the game engine. A raw file — a `.psd`, a `.blend`, a `.wav` — usually isn't used directly; the engine **imports** it, converting it into an engine-ready format and applying settings along the way (compression, size limits, format conversion).

---

## 39.2 Import Settings

Most engines expose per-asset import settings that significantly affect both quality and performance:

- **Textures** — compression format, max resolution, whether mipmaps are generated (smaller versions used automatically at a distance).
- **Models** — scale, whether animations are included, how many polygons are kept (see also Lesson 40 on LOD).
- **Audio** — compression format and quality, and whether a clip streams from disk or loads fully into memory.

Getting these wrong is a common source of avoidable performance problems — importing a 4K texture for something that only ever appears as a small icon wastes memory for no visible benefit.

---

## 39.3 Naming Conventions and Folder Organization

As a project grows, a consistent naming and folder structure becomes essential for a team to find and reuse assets efficiently. Common conventions include:

- Prefixing assets by type (e.g. `T_` for textures, `SM_` for static meshes, `M_` for materials).
- Grouping assets by feature or area rather than by type alone (e.g. a `Player/` folder containing that character's mesh, textures, and animations together).
- Keeping source files (`.psd`, `.blend`) separate from the exported, engine-ready versions, so the pipeline that produced an asset is always traceable.

---

## 39.4 Automating the Pipeline

For larger projects, manually re-importing and re-exporting assets doesn't scale. Teams often automate parts of the pipeline:

- **Auto re-import** — engines can watch a source file and automatically re-import it whenever it's saved from the external tool.
- **Build scripts** — automated steps that validate assets (checking texture sizes, naming conventions) before they're allowed into the project.
- **Asset bundling** — packaging groups of assets together so they can be loaded, updated, or downloaded as a unit, which also plays into how a finished game is eventually built (Lesson 42).

A well-organized asset pipeline saves enormous time over the life of a project, since art and audio content tends to be revised constantly right up until launch.

[Previous](./[38]-Version-Control-for-Game-Projects.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[40]-Performance-Optimization-for-Games.md)
