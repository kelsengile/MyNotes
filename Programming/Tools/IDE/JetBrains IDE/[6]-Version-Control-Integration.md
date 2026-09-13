[Previous](./[5]-Customizing-The-JetBrains-Environment.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[7]-Databases-And-Built-In-Tooling.md)

*Customization And Productivity*

# Lesson 6 - Version Control Integration

## 6.1 Git Integration Basics

JetBrains IDEs detect a Git repository automatically (via the `.git` folder) and expose it through the **Git** tool window and the **VCS** menu — no separate Git GUI application needed.

Visual indicators appear directly in the editor gutter:

```
 12   unchanged line
 13 │ modified line          ← blue bar
 14 + newly added line       ← green bar
 15   unchanged line
      (deleted lines show as a red marker between lines)
```

The **Git** tool window has tabs for:

- **Log** — the full commit history as a graph, with branches and merges visualized.
- **Console** — a text log of every Git command the IDE runs under the hood, useful for learning the underlying CLI equivalents.
- **Local Changes** — files modified but not yet committed, grouped by changelist.

---

## 6.2 Commit, Push, And Pull From The IDE

The Commit tool window (`Ctrl/Cmd+K`) lists every changed file with checkboxes, letting you stage a subset of changes rather than committing everything at once.

```
Commit
 ☑ src/main/UserService.java
 ☑ src/main/UserRepository.java
 ☐ src/test/UserServiceTest.java   ← left unchecked, stays uncommitted

 Commit Message:
 ┌─────────────────────────────────────────┐
 │ Add email validation to UserService       │
 └─────────────────────────────────────────┘
 [Commit]  [Commit and Push]
```

Key actions and where to find them:

| Action | Shortcut | Notes |
|---|---|---|
| Commit | `Ctrl/Cmd+K` | Opens the commit dialog with a diff preview per file |
| Push | `Ctrl/Cmd+Shift+K` | Sends committed changes to the remote |
| Update Project (Pull) | `Ctrl/Cmd+T` | Fetches and merges/rebases remote changes |
| View Diff | Double-click a changed file | Side-by-side comparison with inline editing |

The IDE also shows a **per-line "Annotate"** view (right-click the gutter → Annotate with Git Blame) revealing who last changed each line and in which commit — handy for tracking down when a bug was introduced.

---

## 6.3 Resolving Merge Conflicts Visually

When a merge or rebase produces conflicts, JetBrains opens a three-pane **Merge Conflicts** view instead of leaving raw `<<<<<<<` markers in your file:

```
┌───────────────┬───────────────┬───────────────┐
│  Your Version  │     Result     │ Their Version  │
│  (Left)        │   (Middle)     │  (Right)       │
├───────────────┼───────────────┼───────────────┤
│ return a + b;  │  <choose one>  │ return a * b;  │
└───────────────┴───────────────┴───────────────┘
        [Accept Left]              [Accept Right]
```

- Click **Accept Left/Right** per conflicting block, or manually edit the middle "Result" pane to combine both.
- Non-conflicting changes from both sides are merged automatically, so you only review the lines that actually clash.
- After resolving all blocks, click **Apply** to finish the merge — the file is saved and marked resolved in the Git tool window.

This visual approach avoids the common mistake of accidentally leaving conflict markers (`<<<<<<< HEAD`) committed into the codebase.

---

## 6.4 Local History As A Safety Net

Separate from Git, JetBrains IDEs keep their own **Local History** — a running record of every change made to a file, even before it's saved or committed, stored locally by the IDE itself.

Access it via right-click a file or folder → **Local History → Show History**.

```
Local History: UserService.java
 ● 10:42 AM  "Extracted method validateEmail"     [View] [Revert]
 ● 10:15 AM  "Renamed variable temp → userEmail"   [View] [Revert]
 ● 09:58 AM  Initial state                          [View] [Revert]
```

Why this matters:

- It works even with **no Git repository at all** — useful for scratch files or quick scripts.
- It can recover a file to any prior state, even changes never committed or staged.
- It survives accidental deletions of uncommitted work, since the IDE tracked changes independently of the file system save events.

Local History is not a replacement for Git — it isn't shared with collaborators and isn't kept forever — but it's a valuable last resort when you need to undo further back than the in-editor Undo (`Ctrl/Cmd+Z`) stack allows.

[Previous](./[5]-Customizing-The-JetBrains-Environment.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[7]-Databases-And-Built-In-Tooling.md)
