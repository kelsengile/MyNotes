[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Meson

Meson is a modern build system generator focused on speed and ease of use. Like CMake, it reads its own project description and generates native build files — by default, it generates Ninja files.

Download: [https://mesonbuild.com/](https://mesonbuild.com/)

---

## What Is Meson?

Meson was created partly as a reaction to the complexity of tools like CMake and Autotools, aiming for a simpler, more readable configuration language and sensible defaults out of the box. It's used by large projects including GNOME, GStreamer, and systemd.

---

## A Minimal meson.build

```meson
project('my_app', 'cpp')
executable('my_app', 'main.cpp')
```

---

## Core Commands

```bash
meson setup build            # Configure the project into a build directory
meson compile -C build       # Compile the project
meson test -C build          # Run the project's test suite
meson configure build        # View or change configuration options
ninja -C build                # Equivalent to `meson compile`, since Ninja does the actual build
```

---

## Meson and Ninja Together

Meson generates the project description; Ninja executes it. You'll rarely call Ninja commands directly by name — `meson compile` and `meson test` wrap the underlying Ninja invocations so you don't need to know Ninja's syntax at all.

---

## Example Walkthrough

```bash
meson setup build
meson compile -C build
meson test -C build
```

Configures a fresh `build` directory, compiles the project, and runs its tests — the standard three-step Meson workflow.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
