[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Nano

Nano is a simple, beginner-friendly terminal text editor. Unlike Vim, it has no modes — you type directly, and common commands are shown at the bottom of the screen, making it a common default for quick edits and a frequent recommendation for newcomers to the terminal.

Download: [https://www.nano-editor.org/](https://www.nano-editor.org/)

---

## What Is Nano?

Nano is modeless: whatever you type is inserted directly into the document, and commands are triggered with `Ctrl` key combinations rather than switching modes the way Vim does. This makes it immediately usable without learning a new mental model, at the cost of being less efficient for very fast, complex editing once you're experienced — which is exactly the trade-off that makes it a common recommendation for people who just need to quickly edit a config file.

---

## Core Commands

```bash
nano file.txt          # Open a file for editing
```

**Inside Nano (the bottom bar shows a live reminder of these):**

| Shortcut | Action |
|---|---|
| `Ctrl+O` | Write out (save) the file |
| `Ctrl+X` | Exit |
| `Ctrl+K` | Cut the current line |
| `Ctrl+U` | Paste (uncut) |
| `Ctrl+W` | Search (Where is) |
| `Ctrl+\` | Search and replace |
| `Ctrl+G` | Show help |
| `Ctrl+_` | Go to a specific line number |
| `Alt+U` | Undo |
| `Alt+E` | Redo |

The `^` symbol shown in Nano's own help bar means Ctrl — `^O` means Ctrl+O.

---

## Saving and Exiting

```
Ctrl+O    (write out / save)
Enter     (confirm the filename)
Ctrl+X    (exit)
```

Unlike Vim's single `:wq`, Nano separates saving and exiting into two distinct key combinations — pressing `Ctrl+X` on an unsaved file will prompt to save before quitting, so exiting without saving still requires an explicit "no" to that prompt.

---

## Searching and Replacing

```
Ctrl+W
error
Enter
```

Searches forward for "error"; pressing `Ctrl+W` again and Enter repeats the last search. `Ctrl+\` opens search-and-replace, prompting for a search term and then a replacement, with an option to replace all occurrences or confirm each one individually.

---

## Cut, Copy, and Paste

```
Ctrl+K      (cut the current line into a buffer)
Ctrl+K      (cut more lines — they accumulate in the same buffer if done consecutively)
Ctrl+U      (paste everything cut)
Alt+6       (copy the current line without cutting it)
```

Repeated `Ctrl+K` presses on consecutive lines accumulate into a single cut buffer, so cutting five lines in a row and pasting once restores all five together — a common way to move a block of text.

---

## Line Numbers and Syntax Highlighting

```bash
nano -c file.txt          # Show the cursor's line/column position
nano -l file.py            # Enable line numbers for this session
```

Nano supports basic syntax highlighting for many languages out of the box, activated automatically based on file extension when a matching syntax definition is installed.

---

## Common Gotchas

- Getting "stuck" less often than Vim, but the reverse problem exists too: experienced terminal users used to modal editors sometimes find Nano's Ctrl-combo-heavy interface awkward for very fast edits.
- Line wrapping: Nano wraps long lines for display by default, which can be visually confusing when editing files with genuinely long lines (like minified code) — `nano -w` disables this wrapping.

---

## Example Walkthrough

```
nano config.txt
Ctrl+W
timeout
Enter
[edit the value]
Ctrl+O
Enter
Ctrl+X
```

Opens a config file, searches for a specific setting, edits its value, saves, and exits — a typical quick edit that Nano is especially well suited for.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Terminal Editors/Vim.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Vim

Vim is a powerful, highly configurable terminal-based text editor descended from the older `vi`. It's famous for its modal editing model, which lets experienced users edit text extremely quickly without ever touching a mouse — and famous among beginners for being notoriously tricky to exit.

Download: [https://www.vim.org/](https://www.vim.org/)

---

## What Is Vim?

Vim's biggest departure from other editors is its **modes**. In Normal mode, keys are commands (navigation, deletion, copying) rather than characters to type. You switch to Insert mode to actually type text, then back to Normal mode to navigate or issue more commands. This design lets almost every key do something useful for editing without needing modifier keys for most actions, which is what enables Vim's famous editing speed once the muscle memory is built.

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

## Vim's Modes

| Mode | Purpose | How to Enter |
|---|---|---|
| Normal | Navigation and commands (default) | `Esc` from any other mode |
| Insert | Typing text directly | `i`, `a`, `o` from Normal mode |
| Visual | Selecting text | `v` (character), `V` (line), `Ctrl+v` (block) |
| Command-line | Running `:` commands (save, quit, search/replace) | `:` from Normal mode |

Understanding that these are genuinely separate modes — not just a mental framing — is the key to Vim: pressing `dd` while in Insert mode just types the letter "d" twice, since commands only mean something in Normal mode.

---

## Movement (The Core Efficiency Gain)

```
h j k l     Left, down, up, right
w           Jump to the start of the next word
b           Jump back to the start of the previous word
0           Jump to the start of the line
$           Jump to the end of the line
gg          Jump to the top of the file
G           Jump to the bottom of the file
:42         Jump to line 42
```

Vim's movement commands compose with action commands — `dw` deletes to the end of a word, `d$` deletes to the end of the line, `3dd` deletes 3 lines — this composability (verb + count + motion) is the real source of Vim's editing speed, far more than any individual keybinding.

---

## Editing Commands

```
x           Delete the character under the cursor
dd          Delete the current line
yy          Yank (copy) the current line
p / P       Paste after / before the cursor
u           Undo
Ctrl+r      Redo
.           Repeat the last change
```

The `.` command deserves special mention: it repeats whatever the last change was, which combined with movement makes many repetitive edits (like "delete this word, on this exact pattern, five times") extremely fast without scripting anything.

---

## Search and Replace

```
/pattern         Search forward
?pattern         Search backward
n / N            Next / previous match
:%s/old/new/g    Replace every occurrence of "old" with "new" in the whole file
:%s/old/new/gc   Same, but confirm each replacement individually
```

`:%s/old/new/g` is one of the most-used Vim commands in practice — `%` means the whole file, `s` is substitute, and the trailing `g` means every occurrence per line rather than just the first.

---

## Visual Mode

```
v          Start character-wise visual selection
V          Start line-wise visual selection
Ctrl+v     Start block-wise (column) visual selection
d          (after selecting) delete the selection
y          (after selecting) yank the selection
```

Block-wise visual mode (`Ctrl+v`) is particularly powerful for editing a rectangular region across multiple lines at once — for example, adding the same prefix to several consecutive lines.

---

## Configuration: .vimrc

```vim
" ~/.vimrc
set number          " show line numbers
set tabstop=4        " tab width
set expandtab        " use spaces instead of tabs
syntax on            " enable syntax highlighting
```

Vim's behavior is customized through `~/.vimrc`, read at startup — this is where most users configure line numbers, indentation preferences, color schemes, and key remappings to make Vim fit their workflow.

---

## The Famous "How Do I Exit Vim?" Moment

Pressing `Esc` to make sure you're in Normal mode, then typing `:wq` and pressing Enter, saves and exits — this single sequence resolves what is probably the internet's most-repeated Vim question.

---

## Common Gotchas

- Forgetting which mode you're in: typing commands while accidentally still in Insert mode is the most common beginner frustration — `Esc` always returns to Normal mode as a safe default.
- Unsaved changes blocking `:q`: Vim refuses to quit a modified buffer with plain `:q`, requiring either `:wq` (save and quit) or `:q!` (discard changes and quit) — this refusal is a deliberate safeguard, not a bug.

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

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)