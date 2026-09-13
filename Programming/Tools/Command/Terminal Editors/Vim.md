[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# Vim

Vim is a powerful, highly configurable terminal-based text editor descended from the older `vi`. It's famous for its modal editing model, which lets experienced users edit text extremely quickly without ever touching a mouse — and famous among beginners for being notoriously tricky to exit.

Download: [https://www.vim.org/](https://www.vim.org/)

---

## What Is Vim?

Vim's biggest departure from other editors is its **modes**. In Normal mode, keys are commands (navigation, deletion, copying) rather than characters to type. You switch to Insert mode to actually type text, then back to Normal mode to navigate or issue more commands.

---

## Core Commands

```
vim file.txt        # Open a file in Vim
```

**Inside Vim (Normal mode):**
```
i         Enter Insert mode (start typing)
Esc       Return to Normal mode
:w        Save the file
:q        Quit
:wq       Save and quit
:q!       Quit without saving
dd        Delete the current line
yy        Copy ("yank") the current line
p         Paste
/pattern  Search forward for a pattern
```

---

## The Famous "How Do I Exit Vim?" Moment

Pressing `Esc` to make sure you're in Normal mode, then typing `:wq` and pressing Enter, saves and exits — this single sequence resolves what is probably the internet's most-repeated Vim question.

---

## Example Walkthrough

```
vim notes.txt
i
Hello, Vim!
Esc
:wq
```

Opens a file, switches to Insert mode to type a line of text, returns to Normal mode, then saves and quits — the basic loop behind every Vim editing session.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
