[Previous](./[44]-Game-Design-Principles.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[46]-Localization-for-Games.md)

*Best Practices*

# Lesson 45 - Accessibility in Games

## 45.1 Why Accessibility Matters

**Accessibility** means designing a game so it can be played and enjoyed by people with a wide range of abilities, including visual, auditory, motor, and cognitive differences. It's often treated as an afterthought, but it's cheapest and most effective when considered from early in development — many accessibility features (like remappable controls) are far harder to retrofit later than to build in from the start.

---

## 45.2 Visual Accessibility

- **Colorblind modes** — alternative color palettes or additional visual cues (icons, patterns) for information that's otherwise conveyed by color alone (e.g. team colors, health states).
- **Text scaling and contrast** — adjustable UI text size and sufficient contrast against backgrounds for players with low vision.
- **Screen reader support** — reading menu text aloud for blind or low-vision players, most common in menu-heavy or text-heavy games.

---

## 45.3 Motor and Input Accessibility

- **Remappable controls** — letting players reassign any action to any input, since a default control scheme may be physically difficult or impossible for some players.
- **Adjustable timing** — options to slow down or remove time-limited challenges (quick-time events, tight timing windows) for players who need more time to react.
- **Alternative input support** — supporting a range of input devices beyond a standard controller or keyboard/mouse, such as switch controls or eye-tracking hardware.

---

## 45.4 Cognitive and Audio Accessibility

- **Subtitles and captions** — text for spoken dialogue (subtitles) and for important non-dialogue sounds like footsteps or explosions (captions), for deaf and hard-of-hearing players.
- **Clear objectives and reminders** — optional in-game prompts or journals reminding players what to do next, helping players with memory or attention differences stay oriented.
- **Reduced motion options** — settings to minimize screen shake, motion blur, or flashing effects, both for comfort (motion sickness) and for players sensitive to flashing lights (photosensitive epilepsy).

None of these features force any particular experience on players who don't need them — they're almost always implemented as optional settings, widening who can play the game without changing it for anyone who doesn't turn them on.

[Previous](./[44]-Game-Design-Principles.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[46]-Localization-for-Games.md)
