[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

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

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
