[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# Nano

Nano is a simple, beginner-friendly terminal text editor. Unlike Vim, it has no modes — you open it and start typing immediately, with on-screen shortcuts always visible at the bottom of the window.

Download: [https://www.nano-editor.org/](https://www.nano-editor.org/)

---

## What Is Nano?

Nano was designed to be approachable: no modal editing to learn, and every available keyboard shortcut is listed at the bottom of the screen while you work, so you rarely need to consult outside documentation just to save a file.

---

## Core Commands

```bash
nano file.txt        # Open a file (or create it if it doesn't exist)
```

**Inside Nano:**
```
Ctrl+O    Save the file ("Write Out")
Ctrl+X    Exit
Ctrl+K    Cut the current line
Ctrl+U    Paste ("Uncut")
Ctrl+W    Search for text
Ctrl+_    Jump to a specific line number
```

The `^` symbol shown in Nano's own bottom-of-screen menu means the `Ctrl` key — `^O` means `Ctrl+O`.

---

## Why People Reach for Nano

For a quick edit to a config file over SSH — changing one line, adding a comment — Nano's lack of a learning curve makes it the path of least resistance compared to Vim, even for admins who use Vim for heavier editing.

---

## Example Walkthrough

```bash
nano /etc/hosts
```

Opens a system config file directly for editing; typing changes them immediately (no Insert mode needed), then `Ctrl+O` followed by Enter saves, and `Ctrl+X` exits back to the shell.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
