[Previous](./[15]-Event-Driven-Programming.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[17]-Data-Binding-and-UI-Updates.md)

*Events & Interaction*

# Lesson 16 - Handling User Input (Mouse, Keyboard, Shortcuts)

## 16.1 Mouse Events

Mouse interaction goes beyond a simple click: frameworks expose `MouseDown`, `MouseUp`, `MouseMove`, `MouseEnter`/`MouseLeave` (hover), double-click, and scroll-wheel events, each carrying coordinates and which button was involved. Drag-and-drop is typically built from a sequence of these: `MouseDown` on an item, `MouseMove` while held (start the drag), `MouseUp` over a valid target (complete the drop).

---

## 16.2 Keyboard Events

`KeyDown`/`KeyUp` fire for physical key presses; a separate `TextInput`/`KeyPress` event fires for the actual character produced, which differs from the raw key when modifiers, dead keys, or IMEs (input method editors for languages like Japanese or Chinese) are involved. Always handle text input through the character-level event, not by manually mapping key codes to letters, or international keyboards will break.

---

## 16.3 Keyboard Shortcuts and Accelerators

Shortcuts (accelerators) bind a key combination to a command, usually declared alongside a menu item so the shortcut is both discoverable and functional:

```csharp
new MenuItem("Save") { Shortcut = "Ctrl+S" }
```

Follow platform conventions rather than inventing your own: `Ctrl` is the primary modifier on Windows/Linux, `Cmd` on macOS. Most frameworks let you declare one shortcut and have it map to the correct modifier automatically.

---

## 16.4 Focus

Only one control has keyboard **focus** at a time, determining where key events go. Users expect `Tab`/`Shift+Tab` to move focus between controls in a logical order, and expect the focused control to be visually indicated (an outline or highlight) — both are essential for keyboard-only and accessibility-tool usage, covered further in Lesson 42.

[Previous](./[15]-Event-Driven-Programming.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[17]-Data-Binding-and-UI-Updates.md)
