[Previous](./[11]-Collision-Detection-And-Response.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[13]-Scripting-And-Game-Logic.md)

*Physics And Interaction*

# Lesson 12 - Input Handling

## 12.1 Input Devices

Engines need to read input from a wide range of devices, including:

- **Keyboard and mouse** — the standard for PC games.
- **Gamepads/controllers** — common on consoles and often supported on PC as well.
- **Touchscreens** — the primary input method for most mobile games, supporting taps, swipes, and multi-touch gestures.

Because these devices behave so differently (a mouse reports continuous 2D movement, a gamepad reports button presses and analog stick positions, a touchscreen reports one or more finger positions), engines provide an **input system** that abstracts away these differences behind a consistent API, so gameplay code doesn't need to be rewritten for every device a game might support.

## 12.2 Polling vs Event-Driven Input

There are two general approaches to reading input:

- **Polling** — the game actively checks the current state of an input device once per frame (e.g., "is the spacebar currently held down right now?"). This is simple and works well for continuous actions like movement, where you care about the state at every frame.
- **Event-driven input** — the input system notifies interested code only when something changes (e.g., "the spacebar was just pressed"), rather than the game having to check every frame. This is efficient and works well for discrete actions like firing a weapon once per press, rather than repeatedly firing every frame the button happens to be held.

Most engines support both approaches side by side, since different kinds of input naturally fit one model better than the other. Continuous movement is usually polled every frame; a menu button click is usually handled as an event.

## 12.3 Input Mapping And Action Systems

Hardcoding "if the W key is pressed, move forward" directly into gameplay code becomes a problem the moment you want to support rebindable controls, multiple input devices, or different keyboard layouts. Modern engines solve this with an **input mapping** (or "action") system: instead of gameplay code checking for specific keys directly, it checks for a named, abstract **action** (like `"Move Forward"` or `"Jump"`), and a separate mapping configuration decides which physical inputs (W key, gamepad stick, touch swipe) trigger that action.

A simplified example of this separation:

```
// Mapping configuration (data, not code):
"Jump" -> Spacebar, Gamepad Button South, Touch Swipe Up

// Gameplay code (never references specific keys directly):
if (input.action_pressed("Jump")) {
    player.jump()
}
```

This indirection is exactly the data-driven philosophy from Lesson 2 applied to input: gameplay logic asks "was Jump pressed?" without caring what physical button the player (or the player's custom key bindings) actually used to trigger it.

## 12.4 Handling Multiple Players

Local multiplayer games (split-screen or shared-input) need to handle input from **multiple distinct sources** at once, each mapped to a different player. Engines typically support this by assigning each connected input device (each gamepad, or designated key groups on a shared keyboard) to a specific **player index**, and routing that device's input only to the corresponding player's character and camera (recall the multi-viewport setup discussed in Lesson 7 for split-screen rendering).

Designing input systems with multiple players in mind from the start — rather than assuming a single global "the player" — is a common lesson learned the hard way by developers who add local co-op to a game late in development, since it often requires restructuring input-handling code that assumed only one source of input would ever exist.

---

[Previous](./[11]-Collision-Detection-And-Response.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[13]-Scripting-And-Game-Logic.md)
