[Previous](./[1]-Installing-JetBrains-IDEs-And-The-Toolbox-App.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[3]-Code-Intelligence-And-Navigation.md)

*Getting Started*

# Lesson 2 - The JetBrains Interface

## 2.1 The Project Tool Window

The Project Tool Window, usually docked on the left, shows your project's file and folder structure. It's your primary way of browsing the codebase outside of the editor.

```
┌───────────────────────────┐
│ ▾ my-project               │
│   ▾ src                    │
│     ▾ main                 │
│       ▸ java                │
│       ▸ resources           │
│   ▾ test                   │
│   ▸ .idea                   │
│     pom.xml                 │
│     README.md               │
└───────────────────────────┘
```

Key behaviors:

- **Multiple view modes** — switch between "Project" (full file tree), "Packages" (logical package structure for Java/Kotlin), or "Scratches and Consoles."
- **Right-click context menu** — create new files/folders, refactor, run tests, or open a terminal at that location.
- **Auto-scroll to source** — enable this so the tool window highlights whatever file is currently open in the editor.

---

## 2.2 The Editor Area And Tabs

The editor area is the large central pane where you read and write code. Each open file gets a tab along the top.

- **Split editors** — drag a tab to the side, or right-click → "Split Right"/"Split Down," to view two files at once (e.g. a header and its implementation).
- **Soft-wrap and gutter icons** — the left gutter shows line numbers, breakpoints, VCS change markers (a colored bar for added/modified/deleted lines), and quick-fix light bulbs.
- **Tab pinning** — pin frequently used files so they aren't pushed out when you open many others.
- **Recent Files (Ctrl/Cmd+E)** — jump back to any recently opened file without touching the Project Tool Window.

---

## 2.3 Tool Windows (Terminal, Run, Version Control)

Tool windows are dockable panels around the editor's edges. They can be toggled, resized, moved, or set to "float" as separate windows.

| Tool Window | Purpose | Default Location |
|---|---|---|
| Project | File tree browser | Left |
| Terminal | Embedded shell (bash, zsh, PowerShell) | Bottom |
| Run | Output of the currently running program | Bottom |
| Debug | Breakpoints, variables, call stack | Bottom |
| Git/Version Control | Commit history, changes, branches | Bottom |
| Structure | Outline of the current file's classes/methods | Left (alt) |

```
┌─────────────┬───────────────────────────────┬────────────┐
│             │                                 │            │
│  Project    │          Editor Area            │  Structure │
│  Tool       │                                 │  (optional)│
│  Window     │                                 │            │
│             │                                 │            │
├─────────────┴───────────────────────────────┴────────────┤
│ Terminal | Run | Debug | Version Control  (bottom docked)  │
└──────────────────────────────────────────────────────────┘
```

Tool windows can be summoned with keyboard shortcuts (e.g. `Alt+F12` for Terminal on Windows/Linux, `Option+F12` on macOS), which is faster than reaching for the mouse once memorized.

---

## 2.4 Navigating Between Files

Because JetBrains IDEs index the whole project, navigation isn't limited to clicking through folders:

- **Search Everywhere** (double-tap `Shift`) — search files, classes, symbols, and even IDE settings from one box.
- **Go to File** (`Ctrl/Cmd+Shift+N`) — jump straight to a file by typing part of its name (fuzzy matching supported, e.g. `usrctrl` can match `UserController.java`).
- **Go to Class/Symbol** (`Ctrl/Cmd+N` / `Ctrl/Cmd+Alt+O`) — jump to a class or symbol definition by name.
- **Navigation history** (`Ctrl/Cmd+Alt+Left/Right`) — move backward and forward through recently visited locations, similar to browser back/forward.

Learning these shortcuts early pays off — most JetBrains power users navigate almost entirely by keyboard rather than the Project Tool Window.

[Previous](./[1]-Installing-JetBrains-IDEs-And-The-Toolbox-App.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[3]-Code-Intelligence-And-Navigation.md)
