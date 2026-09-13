[Previous](./[8]-UI-With-Control-Nodes.md) | [Table of Contents](./[0]-Introduction-to-Godot.md)

*Shipping*

# Lesson 9 - Exporting And Publishing A Godot Project

## 9.1 Export Templates

Before you can export a playable build, Godot needs **Export Templates** — precompiled binaries of the Godot runtime for each target platform (Windows, macOS, Linux, Web, Android, iOS). These are separate from the editor itself, since the editor is a development tool while templates are the stripped-down runtime that ships to players.

You install them via **Editor > Manage Export Templates**, which downloads the set matching your exact editor version. This version-matching matters: templates for Godot 4.2 won't work correctly with a project built in 4.3, so if you ever update the editor mid-project, re-download templates afterward.

Some platforms need extra setup beyond the templates themselves:

- **Android** requires the Android SDK and a configured debug/release keystore.
- **iOS** requires a Mac with Xcode installed, since Apple's toolchain doesn't run on Windows/Linux.
- **Web (HTML5)** templates work out of the box, but exported games must be served over HTTP(S) — opening the exported `.html` file directly from disk won't work due to browser security restrictions.

---

## 9.2 Export Presets For Different Platforms

Once templates are installed, open **Project > Export** to create a **preset** — a saved configuration describing how to build for one specific platform. You can have multiple presets in the same project (e.g. one for Windows, one for Web) and export any of them independently at any time.

Each preset lets you configure things like:

- **Executable name** and icon.
- **Included/excluded files** — by default Godot includes everything in the project, but you can filter out files not needed at runtime (source art files, documentation, etc.) to reduce build size.
- **Platform-specific settings** — e.g. architecture (x86_64 / ARM) for desktop, or permissions for Android.
- **Encryption** — optionally encrypt exported game data to make it harder to extract assets, useful for commercial releases.

```
Project > Export
┌────────────────────────────────┐
│ Presets:                       │
│  ▸ Windows Desktop               │
│  ▸ Linux/X11                     │
│  ▸ Web                           │
│  [Add...]                        │
├────────────────────────────────┤
│ [Export Project]  [Export PCK]   │
└────────────────────────────────┘
```

`Export Project` produces a full standalone build (executable + data); `Export PCK/ZIP` produces just the game's data file, useful if you're distributing updates separately from the runtime executable.

---

## 9.3 Project Settings Before Export

A handful of Project Settings are worth double-checking before your first export, since they're easy to forget until a player runs into them:

- **Display > Window > Size** — your target resolution, and whether the window is resizable.
- **Display > Window > Stretch Mode** — controls how your game scales to different screen sizes/aspect ratios. `canvas_items` (scales UI and visuals together, keeping crispness) and `viewport` (renders at a fixed internal resolution, then scales) are the two most common choices for 2D games.
- **Application > Config > Name** and **Icon** — shown in the OS taskbar/window title and, on some platforms, the application icon.
- **Input Map** (Project Settings > Input Map) — make sure every action your scripts reference (like the `"ui_left"`/`"ui_right"` used in earlier lessons, or any custom actions you've added) is actually defined here; a missing input action fails silently rather than crashing, which can be confusing to debug post-export.
- **Autoloads** (Project Settings > Autoload) — any global/singleton scripts (for save data, game state, audio management) need to be registered here to be available project-wide.

It's good practice to do a full playthrough using an exported build — not just the in-editor Play button — before publishing, since exported builds occasionally surface issues (missing files not marked for export, file-path case sensitivity on some platforms) that don't appear while testing in the editor.

---

## 9.4 Where To Publish (itch.io, Steam, App Stores)

Once exported, where you publish depends on your platform and goals:

- **[itch.io](https://itch.io)** — the most beginner-friendly option. Free to publish, supports Web (HTML5), Windows, macOS, and Linux builds, and has no approval process — ideal for game jams, prototypes, and a first public release.
- **Steam** — requires a one-time developer fee per game and going through Valve's submission/review process, but offers by far the largest PC storefront audience. Godot has no special restrictions here beyond what any Steam game needs (e.g. Steamworks SDK integration via a plugin, if you want achievements/cloud saves).
- **Google Play (Android)** and **Apple App Store (iOS)** — each has its own developer account fee, review process, and platform-specific requirements (privacy policies, content ratings, signing certificates). Exporting for these from Godot is well-supported, but budget extra time for each store's approval workflow, which is outside Godot's control.
- **Self-hosting a Web build** — since HTML5 exports are just static files, you can also host them on any standard web server or static site host (e.g. GitHub Pages) without going through any storefront at all.

For a first project, publishing to **itch.io** is the recommended path — it has the least friction, gives you real players and feedback fastest, and everything you learn about the export process there (presets, testing exported builds, icons/config) transfers directly to any other platform you target later.

---

This concludes the **Introduction to Godot** Topic. From here, the natural next steps are picking a small project — a short platformer or top-down game — and applying Lessons 3 through 9 together end to end, since the concepts (scenes, GDScript, signals, physics, UI, and exporting) are designed to build directly on one another.

[Previous](./[8]-UI-With-Control-Nodes.md) | [Table of Contents](./[0]-Introduction-to-Godot.md)
