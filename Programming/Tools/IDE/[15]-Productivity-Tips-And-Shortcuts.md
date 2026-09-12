[Previous](./[14]-Code-Quality-Tools.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[16]-Choosing-An-IDE.md)

*Productivity And Best Practices*

# Lesson 15 - Productivity Tips And Shortcuts

## 15.1 Essential Keyboard Shortcuts

While exact bindings vary by IDE, a few categories of shortcut are worth learning well because they're used constantly: quick file open (jump to any file by typing part of its name), symbol search (jump to any function or class), go to definition, and toggling the integrated terminal. Learning these for your specific IDE removes far more friction than any other single habit.

| Shortcut category | VS Code (Win/Linux) | VS Code (Mac) |
|---|---|---|
| Quick file open | Ctrl+P | Cmd+P |
| Symbol search | Ctrl+T | Cmd+T |
| Go to Definition | F12 | F12 |
| Toggle terminal | Ctrl+` | Cmd+` |

---

## 15.2 Multi-Cursor Editing

Multi-cursor editing lets you place several cursors at once — for example, one on every line matching a search, or one at every occurrence of a selected word — and type or edit at all of them simultaneously. This turns a repetitive multi-step edit, like adding the same prefix to ten lines, into a single action.

**Example:** given these three lines —

```js
name
age
email
```

selecting all three occurrences and typing `user.` turns them into:

```js
user.name
user.age
user.email
```

— in one keystroke, instead of three separate edits.

---

## 15.3 Macros And Automation

A macro records a sequence of editor actions (keystrokes, commands) so it can be replayed later with a single trigger. This is useful for a repetitive edit that's too specific for multi-cursor editing to handle directly — record the steps once on the first instance, then replay the macro on every remaining instance.

**Example:** cleaning up 40 lines of inconsistently formatted CSV data where each fix requires several different edits (trimming whitespace, reordering two columns, adding a comma) is a poor fit for multi-cursor editing since the edit isn't identical text at each position — but a macro that records "do this sequence of keystrokes once" can be replayed 39 more times with a single shortcut press per line.

---

## 15.4 Managing Multiple Projects

Most IDEs support switching between recently opened projects quickly, and many can open more than one project window at once side by side. For related projects that are frequently worked on together, a multi-root workspace (covered in Lesson 4) is usually more efficient than juggling separate windows.

| Approach | Best for |
|---|---|
| Recent projects list | Jumping between unrelated projects occasionally |
| Multiple windows side by side | Comparing two projects visually at the same time |
| Multi-root workspace | Projects that are edited together constantly (e.g. frontend + backend) |

---

[Previous](./[14]-Code-Quality-Tools.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[16]-Choosing-An-IDE.md)
