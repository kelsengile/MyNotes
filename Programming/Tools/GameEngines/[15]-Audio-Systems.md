[Previous](./[14]-Animation-Systems.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[16]-Asset-Pipelines-And-Resource-Management.md)

*Gameplay Systems*

# Lesson 15 - Audio Systems

## 15.1 Sound Effects vs Music

Game audio generally falls into two broad categories, each with different technical needs:

- **Sound effects (SFX)** are short, discrete clips triggered by specific events — a footstep, a gunshot, a button click, an explosion. They're typically loaded as small, uncompressed or lightly compressed files so they can play back instantly the moment they're triggered, with no perceptible delay between the event and the sound.
- **Music** is longer, continuous, and usually streamed rather than loaded entirely into memory — a background track might run for several minutes, so the engine reads and decodes it in small chunks as it plays rather than holding the whole file in RAM at once (similar in spirit to the streaming asset-loading strategies covered in Lesson 16).

This distinction matters for how an engine's audio system is built: SFX playback needs to prioritize **low latency** (the gap between "trigger" and "sound"), while music playback needs to prioritize **efficient streaming and seamless looping**. Most engines expose separate APIs, or at least separate default settings, for these two use cases rather than treating all audio identically.

## 15.2 Audio Sources And Listeners

Just as rendering needs a camera to define what's seen (Lesson 7), audio needs an analogous concept to define what's *heard*. Engines model this with two components:

- An **audio source** is attached to a GameObject and represents something making a sound — an engine on a car, a torch crackling, a character's footsteps. It has properties like volume, pitch, and which audio clip to play.
- An **audio listener** represents the "ears" of the scene — almost always attached to the active camera or player character. The engine calculates what the listener hears based on the position, distance, and volume of every audio source relative to it.

A scene typically has exactly one active listener at a time. If a scene accidentally ends up with two enabled listeners (for example, after loading a second player into a scene without disabling the first camera), most engines will either only use one of them or produce broken, doubled-up audio — a common early bug for developers new to 3D audio.

## 15.3 3D Spatial Audio

In a 2D game, audio is usually just played at a flat volume. In a 3D game, sound needs to convey *where* it's coming from — this is called **spatial audio**, and it relies on the relationship between an audio source's position and the listener's position (both ultimately just transforms from Lesson 6).

Two effects make spatial audio feel convincing:

- **Attenuation** — sounds get quieter as the distance between the source and the listener increases, typically following a falloff curve the developer can tune (a sound might be at full volume within 2 meters and inaudible beyond 20 meters).
- **Panning** — sounds are mixed differently between the left and right audio channels (or more, in surround setups) based on the direction of the source relative to the listener, so a sound to the listener's left is audibly louder in the left channel.

A simplified pseudocode sketch of how an engine might compute a source's effective volume:

```
function calculate_volume(source, listener) {
    distance = distance_between(source.position, listener.position)
    volume = falloff_curve(distance, source.min_distance, source.max_distance)
    return volume * source.base_volume
}
```

Combined, attenuation and panning let a player close their eyes and still roughly point toward an off-screen enemy based on sound alone — a core part of how 3D games communicate spatial information without relying purely on visuals.

## 15.4 Mixing And Audio Buses

A finished game might have dozens of sounds playing simultaneously — music, ambient background noise, footsteps, weapon fire, UI clicks — and playing them all at a flat, unadjusted volume would produce a chaotic wall of noise. **Mixing** is the process of balancing these sounds relative to each other, and most engines organize this through **audio buses** (sometimes called mixer groups).

Rather than controlling every individual sound's volume independently, sources are routed into buses by category:

```
Master Bus
├── Music Bus
├── SFX Bus
│   ├── Player Bus
│   └── Enemy Bus
└── UI Bus
```

Turning down the `Music Bus` lowers every music track at once without touching sound effects, and a game's settings menu "Music Volume" and "SFX Volume" sliders are almost always just exposing direct control over buses like these. Buses can also carry shared effects — for example, applying a muffling filter to the entire `SFX Bus` when the player enters a menu, so every sound effect is affected consistently without editing each one individually.

---

[Previous](./[14]-Animation-Systems.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[16]-Asset-Pipelines-And-Resource-Management.md)
