[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# Ninja

Ninja is a small, fast build system designed to execute pre-generated build instructions as quickly as possible. Unlike Make or CMake, it's not meant to be written by hand — other tools (commonly CMake or Meson) generate the `build.ninja` file that Ninja actually runs.

Download: [https://ninja-build.org/](https://ninja-build.org/)

---

## What Is Ninja?

Ninja intentionally has a tiny, low-level syntax with almost no built-in logic — no conditionals, no functions. That simplicity is the point: it lets Ninja parse build files and figure out what needs rebuilding extremely fast, which matters a lot on projects with tens of thousands of files.

---

## Core Commands

```bash
ninja                # Build the default target(s) using build.ninja in the current folder
ninja target_name     # Build a specific target
ninja -j4             # Limit to 4 parallel jobs
ninja -n              # Dry run — show what would be built
ninja clean           # Remove build output (if the generator defined this target)
ninja -t targets      # List all available targets
```

---

## Ninja vs. Make

Where a `Makefile` mixes "what to build" with logic like conditionals and functions, a `build.ninja` file only lists build steps and their dependencies — all generated automatically. This trade-off (less flexibility, more speed) is why projects generate Ninja files with CMake or Meson rather than writing them directly.

---

## Example Walkthrough

```bash
cmake -S . -B build -G Ninja
cd build
ninja
```

Uses CMake to generate a `build.ninja` file, then runs Ninja to compile the project — a common pairing in modern C/C++ projects.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
