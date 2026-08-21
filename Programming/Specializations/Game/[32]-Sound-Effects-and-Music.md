[Previous](./[31]-Procedural-Generation.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[33]-UI-UX-for-Games.md)

*Audio & UX*

# Lesson 32 - Sound Effects & Music

## 32.1 Audio Sources and Listeners

Audio in a game engine typically involves two core components:

- **Audio Source** — attached to an object, plays a specific sound clip (a footstep, an explosion, background music).
- **Audio Listener** — represents "the player's ears," almost always attached to the active camera. The engine calculates volume and panning for each audio source based on its position relative to the listener.

A scene should generally have only one active Audio Listener at a time — having multiple active listeners (e.g. after forgetting to disable one on a second camera) causes audio errors or unpredictable mixing.

---

## 32.2 SFX vs Music vs Ambience

- **SFX (sound effects)** — short, discrete sounds tied to specific events (jumping, hitting an enemy, opening a menu).
- **Music** — the score, usually looping tracks that set mood and often change based on game state (calm exploration music vs. intense combat music).
- **Ambience** — continuous background sound establishing a sense of place (wind, distant crowd noise, machinery hum), distinct from music in that it's meant to feel like part of the environment rather than a composed track.

Keeping these as separate categories — often with separate volume controls — gives players meaningful control and makes mixing (32.4) far more manageable.

---

## 32.3 3D Spatial Audio

In 3D games, sound sources can be made **spatial**, meaning their volume and stereo panning change realistically based on distance and direction relative to the listener:

- **Attenuation** — volume decreasing with distance, usually following a curve you can configure (linear, logarithmic, custom).
- **Panning** — a sound to the player's left plays louder in the left speaker/ear, and vice versa.

2D UI sounds and music are typically kept **non-spatial** (flat, same volume regardless of camera position), since they aren't meant to represent a physical sound source in the world.

---

## 32.4 Audio Mixing Basics

**Mixing** balances all these audio sources so nothing overwhelms or gets lost. Practical basics:

- **Mixer groups/buses** — routing all SFX through one group, music through another, allowing independent volume control (and player-facing volume sliders) per category.
- **Ducking** — temporarily lowering music/ambience volume when important dialogue or a key sound effect plays, so it isn't drowned out.
- **Avoiding clipping** — too many simultaneous loud sounds can distort audio output; limiting how many instances of a sound can play at once helps (e.g. capping simultaneous gunshot sounds during a large firefight).

[Previous](./[31]-Procedural-Generation.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[33]-UI-UX-for-Games.md)
