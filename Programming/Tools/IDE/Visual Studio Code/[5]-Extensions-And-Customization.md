[Previous](./[4]-Debugging-In-VS-Code.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[6]-Source-Control-With-Git-In-VS-Code.md)

*Extending And Customizing*

# Lesson 5 - Extensions And Customization

## 5.1 The Extensions Marketplace

The Extensions view (Activity Bar → 🧩, or `Ctrl/Cmd+Shift+X`) connects to the Visual Studio Code Marketplace, hosting hundreds of thousands of extensions ranging from official Microsoft language support to small community-built tools.

```
Extensions: Search "python"
┌───────────────────────────────────────┐
│ Python                          [Install]│
│ Microsoft ★★★★★ 100M+ downloads          │
│ IntelliSense, linting, debugging...      │
├───────────────────────────────────────┤
│ Pylance                          [Install]│
│ Microsoft — fast language server         │
└───────────────────────────────────────┘
```

Things worth checking before installing an extension:

- **Publisher** — prefer verified publishers (a blue checkmark) for core language support, especially official ones like Microsoft, Google, or a framework's own team.
- **Download count and rating** — a strong signal of reliability, though not a guarantee.
- **Last updated date** — an extension untouched for years may not support the current VS Code API or language version.
- **Workspace recommendations** — a project can define recommended extensions in `.vscode/extensions.json`, which prompts anyone opening the folder to install them.

```json
// .vscode/extensions.json
{
  "recommendations": ["dbaeumer.vscode-eslint", "esbenp.prettier-vscode"]
}
```

---

## 5.2 Popular Extensions By Language

While the exact best set changes over time, certain extensions are near-universal starting points for their ecosystem:

| Language/Framework | Common Extensions |
|---|---|
| Python | Python, Pylance, Jupyter |
| JavaScript/TypeScript | ESLint, Prettier |
| Web (HTML/CSS) | Live Server, Tailwind CSS IntelliSense |
| C/C++ | C/C++ (Microsoft), CMake Tools |
| Java | Extension Pack for Java |
| Go | Go (Google) |
| Docker | Docker, Dev Containers |
| General productivity | GitLens, Error Lens, Path Intellisense |

**Extension Packs** — a single install that bundles several related extensions together (e.g. "Extension Pack for Java" installs the language server, debugger, test runner, and Maven/Gradle support in one step) — are usually the fastest way to get a language fully set up.

---

## 5.3 Themes And Icon Packs

Themes and icon packs are installed exactly like any other extension but only affect appearance, not behavior.

- **Color Themes** (`Ctrl/Cmd+K, Ctrl/Cmd+T`) — recolor the editor and UI. Popular examples include One Dark Pro, Dracula, and GitHub Theme.
- **File Icon Themes** (Command Palette → "File Icon Theme") — give each file type a distinct icon in the Explorer (e.g. a React logo for `.jsx` files). Material Icon Theme and Seti are common choices.
- **Product Icon Themes** — less commonly changed, these restyle the small UI icons (like the ones in the Activity Bar) rather than file icons.

```
Before:              After (Material Icon Theme):
📄 app.js             🟨 app.js   (JS-colored icon)
📄 style.css          🟦 style.css (CSS-colored icon)
📄 index.html         🟧 index.html (HTML-colored icon)
```

Themes are purely cosmetic, but distinct file icons meaningfully speed up scanning a large file tree, since you can recognize a file's type by its icon color before reading the extension.

---

## 5.4 User And Workspace Settings (settings.json)

VS Code settings exist at two levels, and workspace settings always override user settings when both define the same key:

- **User Settings** — apply globally, to every project you open. Stored in a global `settings.json`.
- **Workspace Settings** — apply only to the current folder/workspace. Stored in `.vscode/settings.json` inside the project, and can be committed to version control so the whole team shares the same settings.

Settings can be edited through the GUI (**Settings** icon, or `Ctrl/Cmd+,`) or directly as JSON (Command Palette → "Preferences: Open User Settings (JSON)").

```json
// .vscode/settings.json (workspace-level)
{
  "editor.formatOnSave": true,
  "editor.tabSize": 2,
  "files.exclude": {
    "**/node_modules": true
  },
  "python.defaultInterpreterPath": "./venv/bin/python"
}
```

A common team workflow: commit `.vscode/settings.json` and `.vscode/extensions.json` together, so that anyone who clones the repository and opens it in VS Code gets consistent formatting rules and a prompt to install the same extensions — closely mirroring what JetBrains' Settings Sync (Lesson 5.4 of the JetBrains Topic) achieves through an account instead of the repository itself.

[Previous](./[4]-Debugging-In-VS-Code.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[6]-Source-Control-With-Git-In-VS-Code.md)
