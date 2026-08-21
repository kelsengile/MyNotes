[Previous](./[21]-Cameras-in-3D.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[23]-Animation-Systems.md)

*Gameplay Systems*

# Lesson 22 - Player Input & Controls

## 22.1 Input Devices

Games typically support multiple input devices, each with different characteristics:

- **Keyboard & mouse** — precise, many simultaneous inputs available, standard for PC.
- **Gamepad/controller** — analog sticks provide smooth, variable-intensity input (unlike a keyboard's on/off keys), standard for console and many PC games.
- **Touch** — taps, swipes, and gestures, standard for mobile.

Designing controls with your target device in mind from the start avoids awkward retrofits later — a control scheme built purely around precise mouse aiming won't translate well to touch.

---

## 22.2 Polling vs Event-Driven Input

- **Polling** — checking the current state of an input every frame inside `Update` (e.g. `if (Input.GetKey(KeyCode.Space))`), best for continuous actions like movement.
- **Event-driven** — reacting to a specific input *event* when it happens (e.g. "on button pressed this frame"), best for one-shot actions like jumping or firing.

Most engines support both approaches, and games typically mix them: polling for continuous movement, and one-shot event checks for discrete actions like jumping.

---

## 22.3 Input Mapping / Action Systems

Rather than hardcoding "the W key moves forward" throughout your codebase, modern engines encourage defining abstract **input actions** (like "MoveForward" or "Jump") and mapping them to physical keys/buttons separately:

```
Action "Jump" → Keyboard: Space, Gamepad: South Button (A/Cross)
```

Benefits:

- Lets players **rebind controls** without touching game logic.
- Makes it trivial to support multiple input devices with the same gameplay code, since the code only ever asks "was Jump pressed?" regardless of which physical input triggered it.

---

## 22.4 Handling Multiple Input Sources

When supporting keyboard/mouse and gamepad simultaneously, a few practical considerations:

- **Device switching** — detecting which device the player last used, and updating on-screen prompts (button icons) to match.
- **Dead zones** — a small threshold near a joystick's resting position that's ignored, preventing drift from slightly imperfect analog sticks from being read as movement.
- **Sensitivity settings** — exposing look/aim sensitivity as a player-adjustable option, since comfortable sensitivity varies significantly between players.

[Previous](./[21]-Cameras-in-3D.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[23]-Animation-Systems.md)
