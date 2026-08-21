[Previous](./[1]-What-is-Game-Development.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[3]-Anatomy-of-a-Game-Project.md)

*Getting Started*

# Lesson 2 - Setting Up a Game Engine (Unity, Unreal, Godot)

## 2.1 System Requirements & Installation

Before installing anything, check your machine against the engine's minimum requirements — game engines are resource-hungry, especially for 3D work. In general you'll want:

- A 64-bit OS (Windows, macOS, or Linux, depending on the engine).
- At least 8GB of RAM (16GB+ recommended for Unreal).
- A dedicated GPU with up-to-date drivers for smooth 3D rendering.
- Several GB of free disk space per engine version installed.

---

## 2.2 Installing Unity via Unity Hub

Unity is installed through **Unity Hub**, a launcher that manages multiple Unity versions and projects side by side.

1. Download Unity Hub from Unity's official website.
2. Sign in with (or create) a Unity account.
3. From the "Installs" tab, install a specific **Editor version** — prefer an LTS (Long Term Support) release for stability.
4. When installing, select the modules you need (e.g. Android Build Support, WebGL Build Support) — these can also be added later.

---

## 2.3 Installing Unreal Engine via Epic Games Launcher

Unreal is distributed through the **Epic Games Launcher**.

1. Download and install the Epic Games Launcher.
2. Sign in with (or create) an Epic Games account.
3. Under the "Unreal Engine" tab, install a specific engine version.
4. Optionally install the Unreal Engine Editor's associated source code from GitHub if you plan to modify the engine itself (requires linking your GitHub account to Epic).

---

## 2.4 Installing Godot

Godot is refreshingly simple: it ships as a **single executable** with no installer or launcher required.

1. Download the appropriate build (Standard or .NET/C# support) from Godot's official site.
2. Extract and run the executable — there's nothing to install.
3. Optionally use a version manager (like `gdvm`) if you need to switch between multiple Godot versions for different projects.

---

## 2.5 Creating Your First Project

Regardless of engine, the first-project flow is similar:

1. Open the engine's project manager (Unity Hub, Epic Games Launcher, or Godot's Project Manager).
2. Choose a **template** — a 2D or 3D starter template saves you from configuring render settings from scratch.
3. Name your project and pick a save location. Avoid special characters or very long paths, which can cause build issues later.
4. Open the project and confirm it launches into an empty scene with a default camera — this is your blank canvas for the rest of the course.

[Previous](./[1]-What-is-Game-Development.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[3]-Anatomy-of-a-Game-Project.md)
