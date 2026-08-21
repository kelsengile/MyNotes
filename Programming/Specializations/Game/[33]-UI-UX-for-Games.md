[Previous](./[32]-Sound-Effects-and-Music.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[34]-Particle-Effects-and-Visual-Feedback.md)

*Audio & UX*

# Lesson 33 - UI/UX for Games (HUD, Menus)

## 33.1 HUD vs Menus

- **HUD (Heads-Up Display)** — persistent, in-gameplay UI overlaid on the screen during play, showing information like health, ammo, and the minimap, without interrupting gameplay.
- **Menus** — separate screens (main menu, pause menu, inventory screen) that usually pause or replace active gameplay, and are navigated more deliberately (button clicks, controller navigation) rather than glanced at passively.

Good HUD design shows only what the player actually needs in the moment — cluttered HUDs compete with the gameplay itself for the player's attention.

---

## 33.2 Canvas and UI Systems

Most engines render UI through a dedicated system, often called a **Canvas** (or similar), which handles 2D screen-space rendering separately from the 3D/2D game world:

- **Screen space** — UI is drawn directly to the screen, unaffected by the camera's position in the game world; this is the default for most HUD and menu elements.
- **World space UI** — UI elements placed within the actual 3D/2D world (e.g. a health bar floating above an enemy's head), which does move and scale with the camera like other world objects.

---

## 33.3 Responsive and Scalable UI

Games run on a wide range of screen sizes and resolutions, so UI needs to adapt rather than being built for one fixed resolution:

- **Anchors** — attaching a UI element to a specific edge or corner of its container, so it stays correctly positioned (e.g. top-right) as the screen resizes.
- **Canvas scaling modes** — automatically scaling UI elements to look consistent across different resolutions and aspect ratios, rather than becoming tiny on large screens or overflowing on small ones.
- **Safe areas** — on devices with notches or rounded corners (many phones), keeping critical UI within a "safe" region that's guaranteed to be visible.

---

## 33.4 UX Principles for Games

Beyond visual layout, good game UX considers how the player *experiences* the interface:

- **Feedback** — every player action should produce a clear, immediate response (a button press should visibly react, a purchase should confirm), so players never wonder if their input registered.
- **Consistency** — using the same icons, colors, and interaction patterns throughout, so players can predict how new UI elements will behave based on ones they've already learned.
- **Minimal friction** — reducing the number of clicks/steps needed for common actions, especially ones performed frequently (like re-equipping a favorite weapon).
- **Clarity over cleverness** — a visually striking menu that's confusing to navigate is worse than a plain one that's instantly understandable.

[Previous](./[32]-Sound-Effects-and-Music.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[34]-Particle-Effects-and-Visual-Feedback.md)
