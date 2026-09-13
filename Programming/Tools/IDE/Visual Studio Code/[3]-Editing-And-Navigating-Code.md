[Previous](./[2]-The-VS-Code-Interface.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[4]-Debugging-In-VS-Code.md)

*Core Editor Features*

# Lesson 3 - Editing And Navigating Code

## 3.1 IntelliSense And Autocomplete

IntelliSense is Microsoft's name for VS Code's code completion system. Its depth depends on the language: for JavaScript/TypeScript it's built in natively, while other languages (Python, C++, Java) gain full IntelliSense through an extension that provides a language server.

```
function greet(user) {
    user.  ← IntelliSense pops up here
}
```

- **Suggestion popup** — triggers automatically as you type, or manually with `Ctrl+Space`.
- **Parameter hints** — typing `(` after a function name shows its parameter list and, if available, a doc-comment description.
- **Auto-import** — accepting a suggestion for a symbol not yet imported (e.g. a React component) automatically inserts the needed `import` statement at the top of the file.

This all runs through the **Language Server Protocol (LSP)** — a standard VS Code popularized, where a separate background process ("language server") analyzes your code and feeds IntelliSense, error-checking, and navigation data back to the editor. This is why installing "Python" or "C/C++" extensions is what unlocks full IntelliSense for those languages.

---

## 3.2 Multi-Cursor Editing

Multi-cursor editing lets you type the same edit in several places simultaneously — one of VS Code's most-loved productivity features.

| Action | Shortcut (Win/Linux) | Shortcut (macOS) |
|---|---|---|
| Add cursor above/below | `Ctrl+Alt+Up/Down` | `Cmd+Option+Up/Down` |
| Add cursor at click | `Alt+Click` | `Option+Click` |
| Select next match of word | `Ctrl+D` | `Cmd+D` |
| Select all matches of word | `Ctrl+Shift+L` | `Cmd+Shift+L` |

```
const first = 1;
const second = 2;     ← Ctrl+D twice selects both "const" here and above
const third = 3;         then typing "let " replaces all three at once
```

`Ctrl+D`/`Cmd+D` is especially powerful: it selects the next occurrence of the currently selected word each time you press it, letting you rename a handful of instances of a variable one match at a time while skipping any that shouldn't change — unlike a global Find & Replace.

---

## 3.3 Go To Definition/References

These commands, available via right-click or keyboard shortcut, are powered by the same language server behind IntelliSense.

| Action | Shortcut | Result |
|---|---|---|
| Go to Definition | `F12` | Jumps to where a symbol is declared |
| Peek Definition | `Alt+F12` | Shows the definition inline, without leaving the current file |
| Go to References | `Shift+F12` | Lists every place a symbol is used |
| Go to Type Definition | (Command Palette) | Jumps to the type/interface behind a variable |

```
const total = calculateTotal(items);
                 ↑ F12 jumps here:

function calculateTotal(items) { ... }
```

**Peek** is worth calling out specifically — it opens a small inline panel showing the definition without switching your active file or scroll position, which keeps context when you just need a quick reminder of what a function does.

---

## 3.4 Search And Replace Across Files

The **Search** view in the Side Bar (`Ctrl/Cmd+Shift+F`) searches across every file in the open folder(s), not just the current file.

```
🔍 Search: "getUserById"
   Replace: "getUserByIdentifier"
   ┌─────────────────────────────────────┐
   │ 📁 src/services (3)                   │
   │   userService.js:12                    │
   │   userService.js:45                    │
   │ 📁 src/controllers (1)                 │
   │   userController.js:8                  │
   └─────────────────────────────────────┘
   [Replace All]
```

Useful refinements:

- **Include/exclude patterns** — restrict the search to specific globs, e.g. include `src/**/*.ts` and exclude `**/node_modules`.
- **Match Case / Whole Word / Regex** toggles (the icons inside the search box) — regex support means you can search with patterns like `console\.log\(.*\)` to find every debug log statement.
- **Search editor** — running a search and clicking "Open in editor" produces a full-file, saveable, shareable list of results, useful for auditing something like all TODO comments across a large codebase.

Unlike a simple text search, this is still not the same as "Find Usages" in a full IDE — VS Code's project-wide search is text-based unless the active language extension also wires "Go to References" (3.3) into the same results.

[Previous](./[2]-The-VS-Code-Interface.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[4]-Debugging-In-VS-Code.md)
