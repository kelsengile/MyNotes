[Previous](./[6]-Unreal-Physics-And-Collision.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[8]-Unreal's-Animation-System-(Animation-Blueprints).md)

*Building Features*

# Lesson 7 - UMG - The Unreal Motion Graphics UI Designer

## 7.1 Widget Blueprints

**UMG** (Unreal Motion Graphics) is Unreal's built-in UI system, and a **Widget Blueprint** is its equivalent of a Blueprint class (Lesson 4) specifically for on-screen UI — a HUD, a main menu, an inventory panel.

Created via **Content Browser > Add > User Interface > Widget Blueprint**, each Widget Blueprint has its own class, its own Variables and Functions in a My Blueprint panel, and — distinctively — two dedicated tabs rather than the single Event Graph of a normal Actor Blueprint:

```
WBP_HealthBar (Widget Blueprint)
├── Designer tab   (visual layout — drag/drop widgets, anchoring)
└── Graph tab      (event graph — logic, bindings, same node system as Lesson 4)
```

Widget Blueprints aren't placed directly into a level the way Actors are — they're created and added to the screen at runtime (typically from another Blueprint or C++, via `Create Widget` followed by `Add to Viewport`), which is covered in 7.4.

---

## 7.2 The Designer And Graph Tabs

The **Designer** tab is where visual layout happens — a **Palette** panel lists available widget types to drag onto a canvas, a **Hierarchy** panel shows the resulting parent-child widget tree, and a **Details** panel edits the selected widget's properties, mirroring the Components/Details relationship from the Actor Blueprint Editor:

```
Hierarchy                          Designer Canvas
├── CanvasPanel (root)             ┌─────────────────────┐
│   ├── ProgressBar (health)        │  ███████░░░  75/100    │
│   └── TextBlock ("75/100")         │                        │
└──                                 └─────────────────────┘
```

The **Graph** tab behaves exactly like an Actor Blueprint's Event Graph — same nodes, same execution/data pin rules from Lesson 4.2 — but scoped to this widget, with two UMG-specific additions:

- **Widget references** — any widget given a name in the Designer's Hierarchy panel automatically becomes a variable accessible from the Graph tab (e.g. dragging in the `HealthBar` ProgressBar node directly).
- **Bindings** — a property (like a TextBlock's Text) can be bound to a function that returns its value every frame, keeping UI automatically in sync with underlying data without manually pushing updates on every change.

```
Function: GetHealthText () → Text
[Function Entry] ──▶ [Get Player Health] ──▶ [Format Text: "{0}/100"] ──▶ [Return Node]
```

Bindings are convenient but re-run every frame like a Tick, so for UI that updates rarely (a score that only changes on events) it's generally more efficient to push the update explicitly from an event (e.g. `OnScoreChanged`) rather than binding, reserving bindings for values that genuinely need constant polling.

---

## 7.3 Common Widgets (Text, Button, Canvas Panel)

A handful of widget types cover the majority of basic UI work:

| Widget | Purpose |
|---|---|
| `Canvas Panel` | Free-form container — children positioned by explicit coordinates/anchors; the default root for most screens |
| `Text Block` | Displays static or dynamically-bound text |
| `Button` | Clickable widget exposing an `OnClicked` event, usually wrapping another widget (often a Text Block) as its visible content |
| `Progress Bar` | Fills proportionally based on a 0–1 `Percent` value — the standard health/mana/loading bar widget |
| `Image` | Displays a Texture or Material — icons, backgrounds, portraits |
| `Vertical Box` / `Horizontal Box` | Stack children in a single direction, auto-sizing to content, unlike a Canvas Panel's fixed coordinates |

```
[Button (OnClicked event)] ──▶ [Open Level: "MainMenu"]
```

Container choice matters: a **Canvas Panel** is best for precisely-positioned HUD elements (a health bar pinned to a corner), while a **Vertical/Horizontal Box** better suits lists that need to grow or reflow (an inventory list, a settings menu) since children reposition automatically as items are added or removed rather than needing manual coordinate management.

---

## 7.4 Adding Widgets To The Viewport

A Widget Blueprint has no on-screen presence until it's explicitly instantiated and added, typically from a Level Blueprint, an Actor's Blueprint (like the PlayerController), or C++:

```
[Event BeginPlay]
      │
      ▼
[Create Widget] ── Class: WBP_HealthBar ──▶ [Add to Viewport] ──▶ [Set HUDReference]
```

- **Create Widget** — instantiates the Widget Blueprint, returning a reference (needed if other logic will later update or remove it).
- **Add to Viewport** — actually displays it on screen, at a specified **Z-Order** (higher values draw on top of lower ones — relevant once multiple widgets, like a HUD and a pause menu, might overlap).
- **Remove from Parent** — called on the widget reference to take it back off screen (a menu closing, a HUD disappearing on death).

A common beginner mistake is calling `Create Widget` repeatedly (e.g. every time a menu button is pressed) without ever removing or reusing the previous instance, silently stacking multiple copies on top of each other — keeping a stored reference to check against, or explicitly removing before creating again, avoids this. For input to reach buttons and other interactive widgets at all, the owning PlayerController generally needs `Set Input Mode UI Only` (or `Game and UI`) alongside `Set Show Mouse Cursor` — otherwise clicks still route to gameplay input as if the widget weren't there.

[Previous](./[6]-Unreal-Physics-And-Collision.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[8]-Unreal's-Animation-System-(Animation-Blueprints).md)