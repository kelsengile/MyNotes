[Previous](./[41]-Testing-and-Debugging-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[43]-Publishing-to-Platforms.md)

*Polishing & Shipping*

# Lesson 42 - Building & Exporting a Game

## 42.1 What a Build Is

A **build** is a compiled, standalone version of the game that can run on a target device without needing the engine's editor installed. Building involves compiling code, packaging assets (often into bundles, see Lesson 39), and producing an executable or platform-specific package ready to run or distribute.

---

## 42.2 Debug vs. Release Builds

- **Debug builds** — include extra information (symbols, verbose logging, assertion checks) that helps during development, at the cost of larger size and slower performance.
- **Release builds** — stripped of debug information and optimized for performance and size, intended for what players actually receive.

Testing performance or final behavior should always happen on a release build — a debug build's extra overhead can make the game appear far slower than it actually is for players.

---

## 42.3 Platform-Specific Build Settings

Each target platform (PC, console, mobile, web) has its own requirements: resolution and aspect ratio handling, input methods supported, minimum OS version, and hardware capabilities. Engines expose per-platform build settings for exactly this reason, and it's common for a project to maintain separate configurations per platform (e.g. different texture compression settings for mobile vs. PC, since mobile GPUs typically support different formats).

---

## 42.4 Build Automation

For any project beyond a solo hobby prototype, manually clicking "build" for every platform on every change doesn't scale. **Build automation** (sometimes via **CI/CD** — continuous integration/continuous deployment) automatically produces a fresh build whenever code is changed, catching build-breaking issues immediately rather than right before a deadline. Automated builds are also commonly wired up to automated testing (Lesson 41), so a broken build and a failing test are caught in the same step.

[Previous](./[41]-Testing-and-Debugging-Games.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[43]-Publishing-to-Platforms.md)
