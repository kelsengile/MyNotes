[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Meson

Meson is a modern build system generator focused on speed and ease of use. Like CMake, it reads its own project description and generates native build files — by default, it generates Ninja files.

Download: [https://mesonbuild.com/](https://mesonbuild.com/)

---

## What Is Meson?

Meson was created in 2012 by Jussi Pakkanen, explicitly as a reaction to the complexity and slow configure times of tools like CMake and Autotools. Its goals were a simpler, more readable configuration language, sensible defaults out of the box, and fast builds by generating Ninja files rather than Makefiles.

It's written in Python but requires no Python knowledge to use, and it has been adopted by a number of large, well-known projects, including GNOME, GStreamer, systemd, Mesa, and QEMU — many of which migrated away from Autotools specifically for Meson's speed and simplicity.

---

## A Minimal meson.build

```meson
project('my_app', 'cpp')
executable('my_app', 'main.cpp')
```

Meson's configuration language, deliberately, is **not** Turing-complete — no arbitrary functions, no complex control flow beyond simple conditionals and loops. This is an intentional design constraint (shared philosophically with Ninja) meant to keep build files easy to read, statically analyzable, and fast to parse.

---

## Core Commands

```bash
meson setup build            # Configure the project into a build directory
meson compile -C build       # Compile the project
meson test -C build          # Run the project's test suite
meson configure build        # View or change configuration options
meson install -C build       # Install built artifacts
meson dist                   # Generate a release tarball of the source
ninja -C build                # Equivalent to `meson compile`, since Ninja does the actual build
```

---

## Meson and Ninja Together

Meson generates the project description; Ninja executes it. You'll rarely call Ninja commands directly by name — `meson compile` and `meson test` wrap the underlying Ninja invocations so you don't need to know Ninja's syntax at all. Meson can also target other backends (see Backends below), but Ninja is the default and by far the most common choice.

---

## Build Options and `meson_options.txt`

Projects declare their own configurable options in a `meson_options.txt` (or `meson.options` in newer versions) file:

```meson
option('enable_tests', type: 'boolean', value: true)
option('backend', type: 'combo', choices: ['opengl', 'vulkan'], value: 'opengl')
```

These are set at configure time or changed later without a full reconfigure:

```bash
meson setup build -Denable_tests=false
meson configure build -Dbackend=vulkan
```

Meson also has many **built-in options** shared by every project, controlling things like optimization level, warning level, and install prefix:

```bash
meson setup build --buildtype=release   # debug (default), release, debugoptimized, minsize, plain
meson setup build --prefix=/usr/local
meson setup build -Dwarning_level=3
```

---

## Dependencies

```meson
zlib_dep = dependency('zlib')
executable('my_app', 'main.cpp', dependencies: zlib_dep)
```

`dependency()` resolves libraries via `pkg-config`, CMake config files, or Meson's own dependency detection, depending on what's available on the system. If a dependency isn't found system-wide, Meson can fall back to a **subproject** (see below) automatically using `fallback:`.

---

## The Wrap System and Subprojects

Meson's most distinctive dependency feature is **WrapDB** — a curated repository of `.wrap` files that describe how to fetch and build a dependency's source from scratch if it isn't already installed:

```bash
meson wrap install zlib
```

This drops a `subprojects/zlib.wrap` file describing where to download zlib's source and how to build it as a Meson subproject, so the whole dependency tree can be built from source in one pass with no external package manager involved — useful for reproducible builds, offline builds, or platforms lacking the dependency in their package manager.

```meson
zlib_dep = dependency('zlib', fallback: ['zlib', 'zlib_dep'])
```

---

## Cross-Compilation with Cross Files

Rather than a CMake-style toolchain file, Meson uses a **cross file** — an INI-style file describing the target system, compilers, and any emulator needed to run tests for that target:

```ini
# arm-cross.ini
[binaries]
c = 'arm-linux-gnueabihf-gcc'
cpp = 'arm-linux-gnueabihf-g++'

[host_machine]
system = 'linux'
cpu_family = 'arm'
cpu = 'armv7'
endian = 'little'
```

```bash
meson setup build --cross-file arm-cross.ini
```

Meson also supports **native files**, which override compiler/tool choices for the build machine itself — handy for CI environments with multiple compiler versions installed.

---

## Backends

Ninja is the default backend, but Meson can generate other project formats:

```bash
meson setup build --backend=ninja      # Default
meson setup build --backend=vs2022     # Visual Studio project
meson setup build --backend=xcode      # Xcode project
```

---

## Introspection

Meson exposes structured, machine-readable information about a configured project — useful for editor/IDE integrations and scripts:

```bash
meson introspect build --targets       # List all build targets as JSON
meson introspect build --buildoptions  # List all configured options
meson introspect build --dependencies  # List resolved dependencies
```

---

## meson devenv and meson dist

```bash
meson devenv -C build         # Drop into a shell with PATH/LD_LIBRARY_PATH set to run the built binaries directly
meson dist -C build           # Package a versioned, reproducible source tarball for release
```

`meson dist` re-verifies that the generated tarball actually builds and passes tests before producing it, which catches "forgot to add a file to the build" mistakes before a broken release goes out.

---

## Meson vs. CMake vs. Autotools

| | Meson | CMake | Autotools |
|---|---|---|---|
| Configuration language | Custom, deliberately simple/non-Turing-complete | Custom, more permissive/scriptable | m4 macros + shell |
| Default backend | Ninja | Platform-dependent (Make, MSBuild, etc.) | Make |
| Configure speed | Fast | Moderate | Slow |
| Dependency fallback system | WrapDB / subprojects | FetchContent / ExternalProject | Bundled source or manual |
| Learning curve | Generally considered gentler | Steeper, especially "old-style" CMake | Steepest |
| Ecosystem maturity | Newer, smaller but growing | Largest, most widespread in C/C++ | Oldest, still common in legacy Unix tooling |

---

## Example Walkthrough

```bash
meson setup build
meson compile -C build
meson test -C build
```

Configures a fresh `build` directory, compiles the project, and runs its tests — the standard three-step Meson workflow.

```bash
meson setup build --buildtype=release -Dbackend_option=vulkan
meson compile -C build
meson install -C build --destdir=./dist
```

Configures an optimized release build with a custom project-defined option set, compiles it, and installs the result into a local staging directory.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)