[Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[2]-The-VS-Code-Interface.md)

*Getting Started*

# Lesson 1 - Installing Visual Studio Code

## 1.1 Downloading And Installing VS Code

VS Code is available for Windows, macOS, and Linux, all from a single download page.

```
code.visualstudio.com/download
 ├── Windows   → .exe (User or System installer)
 ├── macOS     → .zip (Intel or Apple Silicon build)
 └── Linux     → .deb / .rpm / Snap / tarball
```

Installation notes worth knowing before you click through the installer:

- **Windows** — the installer offers a checkbox for "Add to PATH" and "Add 'Open with Code' to context menu." Enable both — the PATH entry lets you type `code .` from any terminal to open the current folder in VS Code.
- **macOS** — after unzipping, drag `Visual Studio Code.app` into `/Applications`. The `code` command isn't added to PATH automatically; run **Shell Command: Install 'code' command in PATH** from the Command Palette (covered in 1.3) to enable it.
- **Linux** — the `.deb`/`.rpm` packages register VS Code with your system's package manager, so future updates can be pulled the same way as any other installed application.

Unlike the JetBrains Toolbox App, VS Code doesn't require a separate installer-manager — the built-in auto-update feature (Settings → "Update: Mode") handles new releases directly.

---

## 1.2 VS Code vs Visual Studio (Naming Confusion)

Despite the similar name, **Visual Studio Code** and **Visual Studio** are two different Microsoft products:

| | Visual Studio Code | Visual Studio |
|---|---|---|
| Type | Lightweight, extensible code editor | Full, heavyweight IDE |
| Platforms | Windows, macOS, Linux | Windows (macOS support discontinued) |
| Primary use | Any language, via extensions | Primarily .NET/C++ enterprise development |
| Cost | Free | Free (Community) to paid (Enterprise) |
| Startup time | Seconds | Can take significantly longer |

If a tutorial or job posting says "VS," always check whether they mean the cross-platform editor (VS Code) or the Windows-centric .NET IDE (Visual Studio) — the two have almost nothing in common under the hood beyond the shared branding.

---

## 1.3 The Command Palette

The Command Palette (`Ctrl/Cmd+Shift+P`) is the single most important shortcut in VS Code — it exposes every command the editor can run, searchable by name, without needing to remember a dedicated keybinding for each one.

```
┌───────────────────────────────────────────┐
│ > format doc                                │
├───────────────────────────────────────────┤
│  Format Document                    Shift+Alt+F │
│  Format Document With...                     │
│  Format Selection                            │
└───────────────────────────────────────────┘
```

Typing `>` (which the Palette pre-fills automatically) searches commands. Two related, equally useful variants:

- **`Ctrl/Cmd+P`** — Quick Open, searches for files by name instead of commands.
- **`Ctrl/Cmd+Shift+O`** — searches for symbols (functions, classes) within the current file.

A good habit for beginners: whenever you don't know the keybinding for something, open the Command Palette and search for it in plain English (e.g. "close all editors," "toggle word wrap") — the result also shows the keybinding, if one exists, so you learn it for next time.

---

## 1.4 Opening Folders And Workspaces

VS Code is folder-centric rather than project-file-centric — there's no `.sln` or `.iml` project file required to get started. Opening a folder (**File → Open Folder**, or `code .` from a terminal) is enough for VS Code to treat it as a project.

- **Single folder** — the most common setup; the folder becomes the root shown in the Explorer side bar.
- **Multi-root workspace** — add multiple, unrelated folders to a single window (**File → Add Folder to Workspace**), useful when a frontend and backend live in separate repositories but you want to work on both at once.
- **`.code-workspace` file** — saving a multi-root setup produces a `.code-workspace` file storing the folder list plus workspace-specific settings, which can be committed or shared with teammates so everyone opens the same layout.

```
my-workspace.code-workspace
{
  "folders": [
    { "path": "frontend" },
    { "path": "backend" }
  ]
}
```

[Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[2]-The-VS-Code-Interface.md)
