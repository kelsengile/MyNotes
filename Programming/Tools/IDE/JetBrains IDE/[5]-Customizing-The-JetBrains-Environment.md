[Previous](./[4]-Running-And-Debugging-Projects.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[6]-Version-Control-Integration.md)

*Customization And Productivity*

# Lesson 5 - Customizing The JetBrains Environment

## 5.1 Keymaps And Shortcuts

The Keymap defines which key combination triggers which action. Found under **Settings → Keymap**, it can be:

- **Switched entirely** — presets exist for VS Code, Eclipse, Vim (via the IdeaVim plugin), and Sublime Text, easing the transition for people coming from another editor.
- **Customized per-action** — search for any action (e.g. "Extract Method") and assign or reassign its shortcut.
- **Searched by shortcut** — press a key combination in the Keymap search box to see what it's currently bound to, avoiding accidental conflicts.

```
Settings → Keymap
 ┌─────────────────────────────────────────┐
 │ Search: "extract method"                 │
 ├─────────────────────────────────────────┤
 │ Refactor This ▸ Extract Method  Ctrl+Alt+M│
 └─────────────────────────────────────────┘
```

**Tip:** learning `Ctrl+Shift+A` ("Find Action") is often more valuable than memorizing shortcuts — it lets you search for and trigger any IDE command by name, including ones without a keyboard shortcut assigned yet.

---

## 5.2 Themes And Editor Appearance

Appearance settings live under **Settings → Appearance & Behavior** (IDE theme) and **Settings → Editor → Color Scheme** (syntax highlighting colors).

- **IDE Theme** — controls the overall UI: toolbars, menus, tool windows (e.g. Darcula/dark, Light, High Contrast).
- **Color Scheme** — controls syntax highlighting in the editor independently of the IDE theme, so you could pair a light IDE theme with a dark editor color scheme.
- **Font settings** — editor font, size, and line height are separate from the UI font, letting you use a ligature-supporting monospace font (e.g. Fira Code, JetBrains Mono) for code while keeping the UI font at system default.
- **Editor → General → Appearance** — toggle things like indent guides, whitespace symbols, and soft-wrapping.

JetBrains publishes its own free font, **JetBrains Mono**, designed specifically for code readability with distinguishable characters (e.g. `0` vs `O`, `1` vs `l`).

---

## 5.3 The Plugins Marketplace

Plugins extend the IDE beyond its built-in features. Access the Marketplace under **Settings → Plugins → Marketplace**.

Popular categories:

| Category | Example Plugins |
|---|---|
| Language support | Rust, Python (for IntelliJ IDEA), Scala |
| Productivity | Key Promoter X (suggests shortcuts for mouse actions you repeat) |
| Code style | .editorconfig, CheckStyle-IDEA |
| AI tooling | GitHub Copilot, JetBrains AI Assistant |
| Version control | GitToolBox (inline blame annotations) |

```
Settings → Plugins → Marketplace
 🔍 Search: "rainbow brackets"
   ┌─────────────────────────────┐
   │ Rainbow Brackets              [Install] │
   │ Colorizes matching bracket pairs         │
   └─────────────────────────────┘
```

Installed plugins can be disabled per-project (not just globally), which keeps unrelated plugins from slowing down projects that don't need them.

---

## 5.4 Settings Sync Across Machines

Settings Sync (built into recent JetBrains IDE versions, replacing the older "Settings Repository" plugin) backs up your keymap, theme, plugins list, and other preferences to your JetBrains Account, then applies them automatically on any other machine you log into.

What typically syncs:

- Keymap and shortcut customizations
- UI theme and editor color scheme
- Installed plugin list
- Code style settings (indentation, import order, etc.)

What does **not** sync by default:

- Project-specific settings (those live in the project's `.idea` folder, which is usually excluded from version control via `.gitignore`)
- Licenses/activation (tied to your JetBrains Account separately)

This is especially useful for developers who switch between a desktop and a laptop, or who reinstall their OS — a fresh IDE install can be brought back to a familiar state in a couple of minutes instead of manually redoing every preference.

[Previous](./[4]-Running-And-Debugging-Projects.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[6]-Version-Control-Integration.md)
