[Previous](./[14]-Menus-Toolbars-and-Status-Bars.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[16]-Handling-User-Input.md)

*Events & Interaction*

# Lesson 15 - Event-Driven Programming

## 15.1 The Event Loop

Unlike a script that runs top-to-bottom and exits, a GUI app spends nearly all its life inside an **event loop**: an infinite loop that waits for input (mouse clicks, key presses, timers, OS messages), dispatches each event to the appropriate handler, then goes back to waiting. The framework usually owns this loop for you — your job is to register handlers, not to write the loop itself.

---

## 15.2 Events and Handlers

An **event** is something that happened (a button was clicked, a window was resized); a **handler** (or listener/callback) is the function that responds to it. Registering a handler subscribes your code to future occurrences of that event:

```javascript
saveButton.addEventListener('click', () => {
  saveDocument();
});
```

```csharp
saveButton.Click += (sender, e) => SaveDocument();
```

---

## 15.3 Event Propagation

In UIs with nested controls, an event can travel through the tree in phases: **capturing** (top-down, from the window to the target) and **bubbling** (bottom-up, from the target back to the window). A child control's handler can stop an event from bubbling further (`e.Handled = true` / `stopPropagation()`), which matters when a parent and child both listen for the same kind of event.

---

## 15.4 Decoupling with Events

Beyond built-in UI events, custom event/messaging systems (event buses, signals/slots in Qt, observer pattern) let separate parts of an app communicate without directly referencing each other — a settings panel can broadcast "theme changed" and any interested component reacts, without the settings panel needing to know who's listening. This decoupling is foundational to the architecture patterns covered in Lesson 32.

[Previous](./[14]-Menus-Toolbars-and-Status-Bars.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[16]-Handling-User-Input.md)
