[Previous](./[12]-Widgets-and-Controls.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[14]-Menus-Toolbars-and-Status-Bars.md)

*UI Fundamentals*

# Lesson 13 - Styling & Theming

## 13.1 Separating Style from Structure

Most modern frameworks separate what a UI *contains* (layout/controls) from how it *looks* (colors, fonts, spacing): CSS in Electron/Tauri, XAML `Styles`/`ResourceDictionaries` in WPF/.NET MAUI, QSS (Qt Style Sheets) in Qt. This separation lets you restyle an entire app without touching its logic.

```css
.primary-button {
  background-color: var(--accent-color);
  border-radius: 6px;
  padding: 8px 16px;
}
```

---

## 13.2 Light and Dark Mode

Users expect apps to respect (or let them override) the OS-level light/dark preference. Frameworks expose an API to detect the current system theme and a way to react when it changes at runtime — define your palette as named variables/resources (`--bg-primary`, `SystemAccentColor`) rather than hardcoding colors, so a theme switch just swaps the variable set.

---

## 13.3 Design Tokens and Consistency

Define a small set of reusable values — spacing scale, color palette, font sizes, corner radii — once, and reference them everywhere instead of repeating literal values. This keeps the UI visually consistent and makes global redesigns (rebranding, accessibility contrast fixes) a matter of editing a handful of definitions.

---

## 13.4 Platform-Native Look and Feel

Cross-platform frameworks face a tension: a UI styled identically everywhere looks "foreign" on each OS, since Windows, macOS, and Linux (GTK) each have distinct visual conventions (button shapes, spacing, default fonts). Some frameworks (Qt, .NET MAUI) auto-adapt to the native look per platform; others (Electron) require deliberate effort to mimic native styling if that fidelity matters for your app.

[Previous](./[12]-Widgets-and-Controls.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[14]-Menus-Toolbars-and-Status-Bars.md)
