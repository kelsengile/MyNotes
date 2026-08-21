[Previous](./[9]-Collections-and-Data-Structures.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[11]-Layouts-and-Containers.md)

*UI Fundamentals*

# Lesson 10 - Windows, Dialogs & the Application Lifecycle

## 10.1 The Main Window

Every GUI app has at least one top-level window, created and shown during startup. A window owns a title, size, position, icon, and a tree of child controls. Most frameworks distinguish the **main window** (closing it typically ends the app) from secondary windows.

---

## 10.2 Modal vs Modeless Dialogs

A **modal** dialog blocks interaction with its parent window until it's closed (e.g. a "Save changes?" prompt) — the user must respond before doing anything else. A **modeless** dialog (e.g. a "Find" panel) stays open alongside the main window, letting the user interact with both. Overusing modal dialogs interrupts workflow, so reserve them for decisions that must be resolved before proceeding.

---

## 10.3 The Application Lifecycle

Desktop apps move through predictable lifecycle stages, and frameworks expose hooks for each:

1. **Launch/Startup** — initialize state, restore saved settings.
2. **Running** — the event loop processes user input (Lesson 15).
3. **Background/Foreground** (more relevant on macOS, where closing all windows doesn't quit the app) — pause/resume expensive work.
4. **Shutdown/Exit** — prompt to save unsaved work, release resources, flush logs.

```csharp
protected override void OnClosing(CancelEventArgs e)
{
    if (HasUnsavedChanges && !ConfirmDiscard())
        e.Cancel = true; // stop the window from closing
}
```

---

## 10.4 Single-Instance Apps

Many apps should only run once per user — a second launch should focus the existing window (or open a file into it) rather than starting a duplicate process. This is implemented with an OS-level lock (a named mutex on Windows, a socket/lock file on Linux, or built-in single-instance APIs in frameworks like Electron and Tauri).

[Previous](./[9]-Collections-and-Data-Structures.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[11]-Layouts-and-Containers.md)
