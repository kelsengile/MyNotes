[Previous](./[1]-Installing-Visual-Studio-Code.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[3]-Editing-And-Navigating-Code.md)

*Getting Started*

# Lesson 2 - The VS Code Interface

## 2.1 The Activity Bar And Side Bar

The **Activity Bar** is the thin, icon-only strip on the far left edge. Each icon switches what's shown in the **Side Bar** next to it.

```
┌───┬──────────────────┐
│ 📄 │ EXPLORER          │  ← Explorer (file tree)
│ 🔍 │                    │
│ 🌿 │                    │
│ 🐞 │                    │
│ 🧩 │                    │
└───┴──────────────────┘
  ↑ Activity Bar   ↑ Side Bar
```

Default Activity Bar icons, top to bottom:

| Icon | View | Purpose |
|---|---|---|
| 📄 Explorer | File tree | Browse and manage project files |
| 🔍 Search | Search | Find/replace across the whole project |
| 🌿 Source Control | Git | Stage, commit, view changes |
| 🐞 Run and Debug | Debug | Breakpoints, variables, call stack |
| 🧩 Extensions | Marketplace | Install/manage extensions |

Both bars can be repositioned (right-click the Activity Bar → "Move Activity Bar") or hidden entirely (`Ctrl/Cmd+B` toggles the Side Bar) to maximize editor space on smaller screens.

---

## 2.2 Editor Groups And Tabs

Open files appear as tabs across the top of the editor area, and the editor area itself can be split into multiple **groups** — independent panes that can each show different files.

- **Split editor** — `Ctrl/Cmd+\` splits the current group; drag a tab to the left/right/top/bottom edge of the editor to split in that direction.
- **Preview mode** — single-clicking a file in the Explorer opens it in italics as a temporary preview, reusing the same tab for the next file you click. Double-clicking (or editing it) "pins" it into a permanent tab.
- **Tab groups layout** — `Ctrl/Cmd+K` then a number key (e.g. `Ctrl/Cmd+K, Ctrl/Cmd+2`) applies preset grid layouts (two columns, three columns, grid) in one step.

```
┌────────────┬────────────┐
│ Group 1     │ Group 2     │
│ app.js      │ app.test.js │
│             │             │
└────────────┴────────────┘
```

---

## 2.3 The Integrated Terminal

The Integrated Terminal (`` Ctrl/Cmd+` ``) opens a real shell session at the bottom of the window, defaulting to your OS's shell (or a configurable one, like Git Bash on Windows).

- **Multiple terminals** — click the `+` icon to open additional terminal instances, and split them side-by-side within the panel.
- **Terminal profiles** — configure different shells (bash, zsh, PowerShell, WSL) as selectable profiles from the dropdown next to the `+` icon.
- **Linked to the editor** — clicking a file path or error message printed in the terminal (e.g. from a stack trace) often becomes a clickable link that jumps straight to that line in the editor.

```
┌──────────────────────────────────────────┐
│ TERMINAL  PROBLEMS  OUTPUT  DEBUG CONSOLE  │
├──────────────────────────────────────────┤
│ $ npm run dev                              │
│ > Local:   http://localhost:5173           │
└──────────────────────────────────────────┘
```

---

## 2.4 The Status Bar

The Status Bar runs along the very bottom of the window and shows contextual information that updates as you work — most items are also clickable shortcuts to related settings.

| Left Side | Right Side |
|---|---|
| Git branch name & sync status | Language mode (e.g. "TypeScript") |
| Errors/warnings count (from Problems) | Line ending style (LF/CRLF) |
| Active extensions' custom items | Encoding (e.g. UTF-8) |
| | Cursor position (Ln, Col) |

For example, clicking the language mode indicator ("TypeScript") opens a picker to change how the current file is interpreted — useful when VS Code guesses the wrong language for a file with an unusual extension.

[Previous](./[1]-Installing-Visual-Studio-Code.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[3]-Editing-And-Navigating-Code.md)
