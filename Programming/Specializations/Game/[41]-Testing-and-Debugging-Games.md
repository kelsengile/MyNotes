[Previous](./[40]-Performance-Optimization-for-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[42]-Building-and-Exporting-a-Game.md)

*Polishing & Shipping*

# Lesson 41 - Testing & Debugging Games

## 41.1 Types of Testing

- **Unit testing** — automatically testing individual, isolated pieces of logic (e.g. a damage-calculation function), catching regressions early and cheaply.
- **Playtesting** — real people playing the game and giving feedback, essential for catching issues automated tests can't, like confusing mechanics or unfun pacing.
- **QA (quality assurance) testing** — systematic testing against a checklist or test plan, aimed at finding bugs, crashes, and edge cases before release.
- **Regression testing** — re-testing previously working features after a change, to catch cases where a fix in one place accidentally broke something else.

---

## 41.2 In-Engine Debugging Tools

Game engines provide tools specifically for inspecting a running game:

- **Debugger/breakpoints** — pausing code execution at a specific line to inspect variable values and step through logic one line at a time.
- **In-engine inspector** — viewing and editing an object's properties live while the game is running, without needing to stop and restart it.
- **Visual debug drawing** — drawing temporary lines, shapes, or text directly in the game world (e.g. an AI's detection radius, a pathfinding route) to visualize otherwise invisible logic.

---

## 41.3 Logging

**Logging** — printing messages from code as it runs — is one of the simplest and most widely used debugging techniques. Effective logging usually distinguishes between severity levels (e.g. info, warning, error), so a flood of expected messages doesn't drown out the ones that actually indicate a problem. Overusing logging in performance-critical code (like the update loop) can itself cause slowdowns, so it's often stripped out or disabled in release builds.

---

## 41.4 Common Bug Categories in Games

- **Logic bugs** — incorrect game behavior (an item giving the wrong effect, a trigger firing at the wrong time).
- **Physics/collision bugs** — objects clipping through geometry, getting stuck, or behaving unpredictably.
- **State bugs** — the game entering an invalid or unintended state (e.g. a menu that can be opened twice, softlocking progress).
- **Platform-specific bugs** — issues that only appear on certain hardware, input devices, or operating systems, which is why testing on actual target platforms (Lesson 43) matters, not just the development machine.

[Previous](./[40]-Performance-Optimization-for-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[42]-Building-and-Exporting-a-Game.md)
