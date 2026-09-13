[Previous](./[7]-Roblox-Physics-And-Constraints.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[9]-Publishing-And-Monetizing-A-Roblox-Experience.md)

*Advanced Experience Design*

# Lesson 8 - GUI With Roblox UI Elements

## 8.1 ScreenGui And Its Children

All 2D user interface in Roblox lives under a **ScreenGui**, a container Instance placed inside `StarterGui` (so it's automatically copied to every player's client when they join) or created dynamically by a LocalScript at runtime.

```
StarterGui
└── HUD (ScreenGui)
    ├── HealthBar (Frame)
    │   └── Fill (Frame)
    ├── ScoreLabel (TextLabel)
    └── PauseButton (TextButton)
```

Because `StarterGui`'s contents are copied into `PlayerGui` (found under each `Player` Instance) once a player spawns, anything you build here is automatically client-specific — one player's health bar changes don't affect anyone else's screen, which mirrors the `CanvasLayer` pattern from the Godot Topic: UI rendered on top of, and independent from, the 3D world.

---

## 8.2 Frames, TextLabels, And Buttons

The core UI-building Instances, all inheriting from `GuiObject`:

| Instance | Purpose |
|---|---|
| `Frame` | A rectangular container, often just a colored panel or a grouping box for other elements |
| `TextLabel` | Static, non-interactive text |
| `TextButton` | Clickable button with text, exposes a `.MouseButton1Click` event |
| `ImageLabel` / `ImageButton` | Displays an image; the button variant is clickable |
| `TextBox` | Editable text input field |
| `ScrollingFrame` | A Frame whose contents can scroll, used for lists/inventories |

Common properties shared across most of these: `Size`, `Position` (both using `UDim2`, covered next), `BackgroundColor3`, `BackgroundTransparency`, and `Visible`.

Roblox also provides **UI Layout** helper Instances — `UIListLayout` (stacks children automatically, similar to Godot's `VBoxContainer`/`HBoxContainer`), `UIGridLayout` (grid arrangement), and `UICorner` (rounds a Frame's corners) — which handle common arrangement patterns without manual position math, the same role Godot's Containers play.

---

## 8.3 UDim2 And Scaling For Different Screens

Roblox UI uses **`UDim2`** for both `Size` and `Position` — a type combining a *scale* component (a fraction of the parent's size, 0 to 1) and an *offset* component (a fixed number of pixels), for both X and Y:

```lua
-- UDim2.new(xScale, xOffset, yScale, yOffset)

-- Half the parent's width, exactly 50px tall, fixed
frame.Size = UDim2.new(0.5, 0, 0, 50)

-- Fills the entire parent regardless of screen size
frame.Size = UDim2.new(1, 0, 1, 0)

-- Centered: anchor point matters here too
frame.AnchorPoint = Vector2.new(0.5, 0.5)
frame.Position = UDim2.new(0.5, 0, 0.5, 0)
```

This scale + offset combination is what makes UI adapt correctly across the huge range of screen sizes Roblox runs on (phones, tablets, desktop monitors) — pure offset values would look correct on one screen and be wildly wrong on another, while pure scale values might keep a button's proportions right but make small text unreadably tiny on a large screen. Mixing both (e.g. "80% of parent width, but never taller than 40px") is standard practice for anything meant to look consistent everywhere.

`AnchorPoint` (a `Vector2` from `(0,0)` to `(1,1)`) determines which point *within the element itself* its `Position` is measured from — `(0.5, 0.5)` means "position refers to my center," which combined with a `Position` of `(0.5, 0, 0.5, 0)` reliably centers an element regardless of its size.

---

## 8.4 Connecting UI To Scripts

UI elements expose events, connected from a **LocalScript** (UI interaction is always client-side — see Lesson 5.2) using the same `:Connect()` pattern used for other Roblox events:

```lua
local button = script.Parent:WaitForChild("PauseButton")
local healthBar = script.Parent:WaitForChild("HealthBar")
local fill = healthBar:WaitForChild("Fill")

button.MouseButton1Click:Connect(function()
    print("Pause button clicked")
    -- e.g. toggle a pause menu Frame's Visible property
end)

local function updateHealthBar(currentHealth, maxHealth)
    local percent = currentHealth / maxHealth
    fill.Size = UDim2.new(percent, 0, 1, 0)
end
```

Because health, score, and most other gameplay values actually live and change on the **server** (Lesson 5.4), a LocalScript updating UI typically listens for a **RemoteEvent** fired by the server, or reads a replicated value (like a `NumberValue` Instance or an attribute on the Player), rather than calculating the value itself:

```lua
-- Server fires this whenever health changes
healthChangedRemote.OnClientEvent:Connect(function(currentHealth, maxHealth)
    updateHealthBar(currentHealth, maxHealth)
end)
```

This keeps the same "server decides, client displays" separation from Lesson 5 consistent all the way through the UI layer — the health bar shows what the server says is true, rather than tracking its own (falsifiable) copy of the player's health.

[Previous](./[7]-Roblox-Physics-And-Constraints.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[9]-Publishing-And-Monetizing-A-Roblox-Experience.md)
