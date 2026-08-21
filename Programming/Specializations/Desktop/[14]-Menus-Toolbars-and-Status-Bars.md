[Previous](./[13]-Styling-and-Theming.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[15]-Event-Driven-Programming.md)

*UI Fundamentals*

# Lesson 14 - Menus, Toolbars & Status Bars

## 14.1 Menu Bars

A menu bar organizes commands into labeled dropdowns (File, Edit, View...). On Windows and Linux the menu bar is typically attached to each window; on macOS it lives at the top of the screen and is shared across the app's windows. Menu items can carry keyboard shortcuts (accelerators), checkmarks for toggle state, and submenus for grouped commands.

```csharp
var fileMenu = new MenuItem("File");
fileMenu.Items.Add(new MenuItem("Open...") { Shortcut = "Ctrl+O", Click = OnOpen });
fileMenu.Items.Add(new MenuItem("Save") { Shortcut = "Ctrl+S", Click = OnSave });
```

---

## 14.2 Toolbars

A toolbar surfaces the app's most frequent commands as clickable icons/buttons, always visible without opening a menu. Good toolbar design favors a small number of high-value actions with clear icons and tooltips over cramming in every possible command — a cluttered toolbar is harder to scan than a well-organized menu.

---

## 14.3 Context Menus

A context menu (right-click menu) shows commands relevant to whatever the user clicked — different for a text selection than for a file in a list. Context menus should be a shortcut to actions that also exist elsewhere in the UI, not the *only* way to reach a feature, since discoverability suffers otherwise.

---

## 14.4 Status Bars

A status bar sits at the bottom of the window and communicates ambient, low-priority information: cursor position, word count, save state, or a background task's progress. It should never be the only place critical information appears, since it's easy to overlook — reserve it for details users check occasionally, not information they need immediately.

[Previous](./[13]-Styling-and-Theming.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[15]-Event-Driven-Programming.md)
