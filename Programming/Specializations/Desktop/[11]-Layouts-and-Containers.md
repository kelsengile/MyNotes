[Previous](./[10]-Windows-Dialogs-and-App-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[12]-Widgets-and-Controls.md)

*UI Fundamentals*

# Lesson 11 - Layouts & Containers

## 11.1 Why Layout Managers Exist

Hardcoding pixel positions for every control breaks the moment a window is resized, a font renders wider on another OS, or the user switches to a display with different DPI. Layout managers (also called layout containers) automatically position and size child controls according to rules, so the UI adapts instead of breaking.

---

## 11.2 Common Layout Types

- **Stack/Box layout** — arranges children in a single row or column (`StackPanel`, `HBox`/`VBox`, flexbox).
- **Grid layout** — arranges children in rows and columns, like a spreadsheet (`Grid`, `QGridLayout`, CSS Grid).
- **Absolute/Canvas layout** — places children at fixed coordinates; appropriate only for specialized cases like a drawing canvas, not general UI.
- **Dock/Anchor layout** — pins children to edges (top toolbar, bottom status bar, filling center content).

```xml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="Auto"/>  <!-- toolbar -->
    <RowDefinition Height="*"/>     <!-- fills remaining space -->
  </Grid.RowDefinitions>
</Grid>
```

---

## 11.3 Nesting Containers

Real UIs nest layouts: a vertical stack might contain a toolbar (horizontal stack) above a grid (main content) above a status bar (horizontal stack). Nesting keeps each section's layout logic simple and independently adjustable.

---

## 11.4 Responsive Sizing

Use relative sizing (`*`, percentages, `flex-grow`) rather than fixed pixel widths wherever content should grow with the window. Set sensible minimum window/control sizes so the UI degrades gracefully rather than clipping content when a user shrinks the window.

[Previous](./[10]-Windows-Dialogs-and-App-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[12]-Widgets-and-Controls.md)
