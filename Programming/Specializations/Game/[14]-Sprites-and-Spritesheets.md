[Previous](./[13]-Transforms-Coordinates-and-Vectors.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[15]-Tilemaps-and-Level-Design.md)

*2D Game Development*

# Lesson 14 - Sprites & Spritesheets

## 14.1 What Is a Sprite?

A **sprite** is a 2D image used to represent a game object — a character, an item, a background element. Sprites are typically drawn with a **SpriteRenderer** (or equivalent) component, which reads an image file and displays it at the object's transform position.

Key properties of a sprite:

- **Pivot point** — the point the sprite rotates and scales around (often its center, or its feet for characters).
- **Pixels per unit** — how many image pixels correspond to one world unit, affecting how large the sprite appears relative to other objects.
- **Sorting layer / order** — determines which sprites draw in front of or behind others when they overlap.

---

## 14.2 Spritesheets and Atlases

Rather than one image file per sprite, artists often pack many related images into a single **spritesheet** (also called a texture atlas):

- Reduces the number of separate texture files the engine has to load and switch between, improving performance.
- Keeps related frames (like a walk cycle) organized together.

Engines provide tools to slice a spritesheet into individual sprites, either automatically (by grid size) or manually (by drawing boundaries around each frame).

---

## 14.3 Sprite Rendering Properties

- **Flipping** — mirroring a sprite horizontally (commonly used so a single "walk right" animation can be reused for walking left).
- **Tinting/color** — multiplying a sprite's color, useful for damage flashes or team-color variations.
- **Layering** — 2D games often use layers to control draw order: background, midground (gameplay), foreground, and UI, each rendered in a fixed order regardless of object position.

---

## 14.4 Frame-Based Animation Basics

Sprite animation works by rapidly swapping between a sequence of frames to create the illusion of motion:

1. A spritesheet contains multiple frames of the same action (e.g. 6 frames of a walk cycle).
2. An **animation clip** defines the order and timing of frames to display.
3. At runtime, the engine swaps the displayed sprite according to that timing, driven by the game loop's `Update`.

This lesson covers the basics; full animation systems, including blending between multiple animations, are covered in Lesson 23.

[Previous](./[13]-Transforms-Coordinates-and-Vectors.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[15]-Tilemaps-and-Level-Design.md)
