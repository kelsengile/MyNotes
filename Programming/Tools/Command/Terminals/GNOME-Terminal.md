[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# GNOME Terminal

GNOME Terminal is the default terminal emulator on GNOME-based Linux distributions like Ubuntu (in its standard flavor) and Fedora Workstation. It's a straightforward, no-frills window for running a shell.

Download: [https://help.gnome.org/users/gnome-terminal/stable/](https://help.gnome.org/users/gnome-terminal/stable/)

---

## What It Does

Like any terminal emulator, GNOME Terminal itself doesn't interpret commands — it hosts whatever shell your user account is configured to use (usually Bash or Zsh) and handles the visual side: rendering text, colors, tabs, and scrollback.

---

## Key Features

- **Tabs** — `Ctrl+Shift+T` opens a new tab running a fresh shell session.
- **Profiles** — configure separate color schemes, fonts, and behavior per profile, then choose one when opening a new window.
- **Search** — `Ctrl+Shift+F` searches scrollback output, handy for finding an error further up in a long build log.
- **Copy/paste without Ctrl+C conflicts** — uses `Ctrl+Shift+C` / `Ctrl+Shift+V` since plain `Ctrl+C` is reserved for interrupting running commands.

---

## Common Keyboard Shortcuts

```
Ctrl+Shift+T   New tab
Ctrl+Shift+N   New window
Ctrl+Shift+W   Close tab
Ctrl+Shift+C   Copy
Ctrl+Shift+V   Paste
Ctrl+Shift+F   Find in scrollback
```

---

## Example Walkthrough

Opening GNOME Terminal, pressing `Ctrl+Shift+T` twice gives three tabs in one window — one to edit code, one to run the app, and one to watch its logs — all without leaving the terminal.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
---
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Konsole

Konsole is the default terminal emulator for the KDE Plasma desktop environment, found on distributions like Kubuntu and openSUSE's KDE spin. It's known for being especially configurable compared to other terminal emulators.

Download: [https://konsole.kde.org/](https://konsole.kde.org/)

---

## What It Does

Konsole hosts your shell of choice and renders its output, the same job as GNOME Terminal or Windows Terminal, but with a heavier emphasis on customization: split views, detachable tabs, and per-profile keyboard shortcuts.

---

## Key Features

- **Split view** — divide a single window into multiple panes, each running its own shell session, arranged horizontally or vertically.
- **Detachable tabs** — drag a tab out into its own window, or merge two Konsole windows' tabs back together.
- **Profiles** — like GNOME Terminal, each profile can define its own shell, working directory, colors, and font.
- **SSH-aware tabs** — Konsole can automatically rename a tab to match the host you've SSH'd into.

---

## Common Keyboard Shortcuts

```
Ctrl+Shift+T          New tab
Ctrl+(               Split view left/right
Ctrl+Shift+(         Split view top/bottom
Ctrl+Shift+W         Close current view
```

---

## Example Walkthrough

Splitting a Konsole window with `Ctrl+(` gives two panes side by side — one running `top` to watch system resources, the other free for regular commands — without needing two separate terminal windows.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
---
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Windows Terminal

Windows Terminal is Microsoft's modern terminal application — the window that hosts shells like CMD, PowerShell, or WSL's Bash. It replaced the old, feature-poor console host with something closer to what Linux and macOS users have had for years.

Download: [https://learn.microsoft.com/windows/terminal/](https://learn.microsoft.com/windows/terminal/)

---

## What Is a Terminal Emulator, and Why Does It Matter?

It's easy to confuse "the terminal" with "the shell," but they're different layers. The **shell** (CMD, PowerShell, Bash) interprets your commands. The **terminal emulator** (Windows Terminal, GNOME Terminal, Konsole) is the window that displays text, handles fonts and colors, and manages tabs and panes — it's just a front-end that can host any shell you choose.

---

## Key Features

- **Multiple tabs and panes** — run CMD, PowerShell, and a WSL Linux shell side by side in one window.
- **Profiles** — each shell gets its own configurable profile (color scheme, starting directory, icon).
- **GPU-accelerated rendering** — smoother scrolling and text rendering than the legacy console host.
- **Settings via JSON** — the entire configuration lives in a human-editable `settings.json` file.

---

## A Sample Profile

```json
{
  "name": "Ubuntu (WSL)",
  "commandline": "wsl.exe -d Ubuntu",
  "startingDirectory": "//wsl$/Ubuntu/home/user",
  "colorScheme": "One Half Dark"
}
```

Adding a profile like this to `settings.json` lets you open a dedicated tab that boots straight into an Ubuntu shell running under WSL.

---

## Example Walkthrough

Opening Windows Terminal and pressing `Ctrl+Shift+D` splits the current pane, letting you run, say, a build command in one pane while tailing a log file in the other — without juggling separate windows.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)