[Previous](./[6]-Unity-Physics-And-Colliders.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[8]-Unity's-Animation-System-(Animator-And-Mecanim).md)

*Building Features*

# Lesson 7 - The Unity UI System (Canvas And RectTransform)

## 7.1 The Canvas And Render Modes

All UGUI (Unity's traditional UI system) elements must live under a **Canvas** GameObject — every UI element is either the Canvas itself or a descendant of it, similar to how Roblox UI must live under a `ScreenGui`, or Godot UI benefits from sitting under a `CanvasLayer`.

A Canvas has three **Render Modes**, controlling how it's positioned relative to the game world:

| Render Mode | Behavior |
|---|---|
| **Screen Space - Overlay** | Rendered directly on top of everything, ignoring cameras entirely. The default and most common choice for HUDs/menus. |
| **Screen Space - Camera** | Rendered on top of the scene but tied to a specific Camera's perspective — useful for effects like UI that scales slightly with camera FOV changes. |
| **World Space** | The Canvas exists as an actual object *in* the 3D world (e.g. a floating health bar above an enemy's head, or a UI panel on an in-game screen/monitor). |

```
Canvas (Screen Space - Overlay)
├── HealthBar (Slider)
├── ScoreText (Text)
└── PauseMenu (Panel)
    ├── ResumeButton
    └── QuitButton
```

For a standard HUD or menu, **Screen Space - Overlay** is the right default; reach for **World Space** specifically when UI needs to exist physically within the 3D scene rather than pinned to the screen.

---

## 7.2 RectTransform And Anchoring

Every UI element uses a **RectTransform** instead of a regular `Transform` — it adds width/height and, crucially, **anchoring**, which controls how an element resizes and repositions relative to its parent as screen size changes.

```
┌─────────────────────────────┐
│  Parent Canvas                │
│  ┌───┐                        │  Anchored top-left:
│  │[X]│                         │  stays fixed distance from
│  └───┘                        │  that corner regardless of
│                                │  screen size
│                    ┌───┐       │
│                    │[X]│       │  Anchored bottom-right
│                    └───┘       │
└─────────────────────────────┘
```

Anchors are set as two points (min and max) between `(0,0)` (bottom-left of parent) and `(1,1)` (top-right), functionally equivalent to Godot's `Control` anchors or Roblox's `UDim2` scale component. Unity's Inspector provides an **Anchor Presets** shortcut (the small square icon in the top-left of the RectTransform section) with common options — corners, edges, stretch, center — so you rarely need to set anchor min/max by hand.

Stretching an element to always fill a percentage of its parent (rather than a fixed pixel size) is done by setting its anchors to different values (e.g. min `(0,0)`, max `(1,1)` stretches to fill the parent entirely) — the same core idea as combining scale and offset in Roblox's `UDim2`.

---

## 7.3 UI Components (Button, Text, Image, Slider)

Unity's built-in UI Components (`Add Component`, or via `GameObject > UI` menu) cover the same ground as the Control nodes and ScreenGui elements from the earlier Topics:

| Component | Purpose |
|---|---|
| `Image` | Displays a sprite/texture as UI |
| `Text` / `TextMeshPro - Text` | Displays text (TextMeshPro is the modern, higher-quality, recommended option) |
| `Button` | Clickable, exposes an `OnClick()` event configurable in the Inspector or via code |
| `Slider` | Draggable value bar — health bars, volume controls |
| `Toggle` | On/off checkbox |
| `InputField` / `TMP_InputField` | Editable text input |

Connecting a Button to code can be done entirely in the Inspector (dragging a GameObject with a public method into the `OnClick()` list) or via script:

```csharp
using UnityEngine;
using UnityEngine.UI;

public class MenuController : MonoBehaviour
{
    public Button startButton;

    void Start()
    {
        startButton.onClick.AddListener(OnStartClicked);
    }

    void OnStartClicked()
    {
        UnityEngine.SceneManagement.SceneManager.LoadScene("Level1");
    }
}
```

`SceneManager.LoadScene()` is Unity's scene-switching call — the direct equivalent of Godot's `change_scene_to_file()` or Roblox's place-teleport functions, swapping the currently active scene for a different one.

---

## 7.4 The New UI Toolkit vs UGUI

Unity has two separate UI systems, and it's worth knowing both exist since documentation and tutorials online are split between them:

| | UGUI (Canvas-based) | UI Toolkit |
|---|---|---|
| **Age** | Original, mature system (what this lesson covers) | Newer system, based on web-like UXML/USS |
| **Structure** | GameObjects + Components (Canvas, RectTransform, etc.) | Separate UXML (structure) + USS (styling) files, closer to HTML/CSS |
| **Best for** | In-game HUDs, world-space UI, most gameplay UI | Editor tooling/custom Editor windows, and increasingly runtime UI in newer projects |
| **Maturity for runtime game UI** | Very mature, huge amount of existing tutorials/assets | Improving steadily each Unity release, but historically weaker for in-game runtime UI |

For learning and most current gameplay UI work, **UGUI remains the practical default** — it has by far the larger body of tutorials, asset store support, and production track record for actual in-game menus and HUDs. UI Toolkit is worth knowing about, especially if you're building custom Editor tools, but isn't necessary to learn first.

[Previous](./[6]-Unity-Physics-And-Colliders.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[8]-Unity's-Animation-System-(Animator-And-Mecanim).md)
