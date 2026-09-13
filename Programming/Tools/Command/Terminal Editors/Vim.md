[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Vim

Vim ("Vi IMproved") is a powerful, highly configurable terminal-based text editor descended from the older `vi`, originally written by Bram Moolenaar and first released in 1991. It's famous for its modal editing model, which lets experienced users edit text extremely quickly without ever touching a mouse — and famous among beginners for being notoriously tricky to exit.

Reference: [https://www.vim.org/](https://www.vim.org/)

---

## What Is Vim?

Vim's biggest departure from ordinary text editors is its **modal editing** model. Rather than every keystroke inserting a character (as in a typical editor), Vim interprets keys differently depending on the current mode:

| Mode | Purpose |
|---|---|
| Normal | Default mode; keys are commands (navigation, deletion, copying) |
| Insert | Typing characters directly into the document |
| Visual | Selecting text (character-wise, line-wise, or block-wise) |
| Command-line | Typing `:` commands for saving, searching/replacing, and configuration |
| Replace | Typing overwrites existing characters instead of inserting |

This design means the entire alphabet is available as a one-key command in Normal mode, letting fluent users navigate and edit text with dramatically fewer keystrokes than moving a mouse or using arrow keys, at the cost of a steep initial learning curve.

`vi`, the original 1976 editor Vim descends from, is itself part of the POSIX specification and guaranteed to exist on essentially every Unix-like system — Vim (and its GUI variant, gVim, and the newer Neovim fork) extends it with syntax highlighting, undo trees, plugins, scripting, and far more.

---

## Core Commands

```
vim file.txt        # Open a file in Vim
vim +42 file.txt     # Open a file with the cursor on line 42
vim -d file1 file2   # Diff mode: compare two files side by side
```

**Inside Vim (Normal mode) — Basic:**
```
i / a       Insert before / after the cursor
I / A       Insert at start / end of the line
o / O       Open a new line below / above and enter Insert mode
Esc         Return to Normal mode
:w          Save the file
:q          Quit
:wq / ZZ    Save and quit
:q!         Quit without saving
```

**Navigation:**
```
h j k l     Left, down, up, right
w / b       Jump forward / backward one word
0 / $       Start / end of the current line
gg / G      Start / end of the file
:42         Jump to line 42
Ctrl+f / Ctrl+b   Page down / up
%           Jump to the matching bracket/parenthesis
```

**Editing:**
```
dd          Delete (cut) the current line
yy          Copy ("yank") the current line
p / P       Paste after / before the cursor
u           Undo
Ctrl+r      Redo
x           Delete the character under the cursor
r X         Replace one character with X
.           Repeat the last change
```

**Search and Replace:**
```
/pattern    Search forward for a pattern
?pattern    Search backward
n / N       Repeat search in same / opposite direction
:%s/old/new/g   Replace all occurrences of "old" with "new" in the whole file
:%s/old/new/gc  Same, but confirm each replacement
```

---

## The Famous "How Do I Exit Vim?" Moment

Pressing `Esc` to make sure you're in Normal mode, then typing `:wq` and pressing Enter, saves and exits — this single sequence resolves what is probably the internet's most-repeated Vim question, and has become something of a running joke in programming culture (referenced in xkcd comics, Stack Overflow's most-viewed question of all time, and countless memes).

---

## Vim's Deeper Feature Set

- **Registers** — beyond the default clipboard-like register, Vim has named registers (`"a`, `"b`, ... `"z`) letting you hold multiple independent clipboards simultaneously, plus special registers for the last search, last inserted text, and more.
- **Macros** — recording a sequence of keystrokes (`qa` ... `q` to record into register `a`, `@a` to replay it) turns Vim into a lightweight scripting tool for repetitive text edits across many lines or files.
- **Marks** — `ma` sets a mark named `a` at the cursor; `` `a `` jumps back to it, useful for bouncing between two points in a large file.
- **Splits and buffers** — `:split` / `:vsplit` divide the window to edit multiple files side by side; `:bn` / `:bp` cycle between open buffers.
- **Vimscript** — Vim's built-in scripting language for configuring behavior, defining custom commands, and writing plugins, configured through `~/.vimrc`.
- **Plugin ecosystem** — plugin managers like `vim-plug` or `Vundle` bring in fuzzy file finders, Git integration, language servers (via plugins bridging Vim to LSP), and full IDE-like functionality on top of the base editor.
- **Text objects** — commands like `di"` (delete inside quotes) or `ca(` (change inside/around parentheses) operate on semantic chunks of text rather than raw characters, a distinctly Vim-flavored editing primitive many other editors have since borrowed.

---

## Vim vs. Neovim

**Neovim** is a 2014 fork of Vim aiming for cleaner internals, better plugin APIs (including native Lua scripting alongside Vimscript), built-in terminal emulation, and easier integration with modern tooling like Language Server Protocol (LSP) clients. Most of Vim's Normal-mode muscle memory transfers directly, and many developers today use Neovim specifically for its more modern plugin and configuration ecosystem while still calling what they're doing "using Vim."

---

## Related Tools

- **vi** — the original, more minimal ancestor, guaranteed present on virtually any Unix system even when Vim isn't installed.
- **Neovim** — the modern, extensible fork described above.
- **Nano** — a much simpler, beginner-friendly terminal editor with on-screen keybinding hints, often recommended as a gentler alternative for quick edits.
- **Emacs** — Vim's long-standing rival terminal-based editor, with a fundamentally different (non-modal, extensively Lisp-scriptable) philosophy.
- **VS Code** (with a Vim extension) — brings Vim keybindings into a modern GUI IDE for users who want the editing model without the terminal-only workflow.

---

## Example Walkthrough

```
vim notes.txt
i
Hello, Vim!
Esc
:wq
```

Opens a file, switches to Insert mode to type a line of text, returns to Normal mode, then saves and quits — the basic loop behind every Vim editing session, and the foundation every more advanced Vim workflow (macros, registers, splits) builds on top of.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)