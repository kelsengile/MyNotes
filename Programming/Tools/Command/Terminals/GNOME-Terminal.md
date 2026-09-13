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
