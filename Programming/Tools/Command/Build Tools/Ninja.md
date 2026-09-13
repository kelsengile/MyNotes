[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Ninja

Ninja is a small, fast build system designed to execute pre-generated build instructions as quickly as possible. Unlike Make or CMake, it's not meant to be written by hand — other tools (commonly CMake or Meson) generate the `build.ninja` file that Ninja actually runs.

Download: [https://ninja-build.org/](https://ninja-build.org/)

---

## What Is Ninja?

Ninja was created by Evan Martin at Google around 2010, originally to speed up incremental builds of Chromium, which had grown large enough that Make's per-file overhead (mostly from re-parsing complex Makefile logic on every invocation) was noticeably slowing down developers' edit-compile cycles.

Ninja intentionally has a tiny, low-level syntax with almost no built-in logic — no conditionals, no functions, no string manipulation. That simplicity is the point: it lets Ninja parse build files and figure out what needs rebuilding extremely fast, which matters a lot on projects with tens of thousands of files. All the "logic" (figuring out compiler flags, finding dependencies, handling platform differences) is expected to happen once, ahead of time, in whatever tool generates the `build.ninja` file.

---

## Core Commands

```bash
ninja                # Build the default target(s) using build.ninja in the current folder
ninja target_name     # Build a specific target
ninja -j4             # Limit to 4 parallel jobs
ninja -j0             # Use as many parallel jobs as there are CPU cores (varies by platform default)
ninja -n              # Dry run — show what would be built without actually building it
ninja -v              # Verbose — print the full command line for every build step
ninja clean           # Remove build output (if the generator defined this target)
ninja -t targets      # List all available targets
```

---

## The build.ninja File Format

Even though Ninja files are meant to be machine-generated, understanding the format helps when debugging a build. It's built from **rules** (how to transform inputs into outputs) and **build statements** (which specific inputs/outputs use that rule):

```ninja
rule cc
  command = gcc -c $in -o $out
  description = Compiling $in

build main.o: cc main.cpp
build app: link main.o
```

- `rule` defines a reusable command template with `$in` (inputs) and `$out` (output) placeholders.
- `build` declares one specific invocation: "to produce `main.o`, run the `cc` rule on `main.cpp`."
- `description` controls what's printed to the console instead of the full raw command, keeping output readable.

Because there's no conditional logic, generators like CMake or Meson pre-compute every possible variation (per-platform flags, per-file overrides) and just emit the final flattened list of `build` statements.

---

## Dependency Tracking

Ninja rebuilds only what's changed, using several mechanisms:

- **Explicit dependencies** — files listed directly in a `build` line (as above).
- **Implicit dependencies** — additional inputs listed after a `|`, which affect whether a rebuild happens but aren't passed to the command as `$in`:
  ```ninja
  build main.o: cc main.cpp | config.h
  ```
- **Dependency files (depfiles)** — for C/C++ compilation, the compiler itself can emit a `.d` file listing every header a `.cpp` file includes. Ninja reads this automatically (`deps = gcc` / `depfile = $out.d`) so that changing a header correctly triggers a rebuild of every `.cpp` file that includes it, without the generator needing to know the full include graph in advance.
- **Order-only dependencies** — listed after `||`, these must exist before a rule runs but don't force a rebuild if they change (commonly used to ensure an output directory exists first).

---

## restat and Avoiding Unnecessary Rebuilds

```ninja
rule cc
  command = gcc -c $in -o $out
  restat = 1
```

`restat = 1` tells Ninja to re-check a file's timestamp *after* a command runs, rather than assuming it changed just because the command executed. This matters for steps like code generation, where a generator might produce byte-identical output — with `restat`, downstream targets that depend on that output won't be needlessly rebuilt.

---

## Pools — Limiting Parallelism for Specific Rules

Ninja normally runs as many build steps in parallel as `-j` allows, but some steps (heavy linking, for instance) use disproportionate memory and shouldn't all run at once even if the CPU has spare cores:

```ninja
pool link_pool
  depth = 2

rule link
  command = g++ $in -o $out
  pool = link_pool
```

This caps concurrent `link` steps at 2, regardless of the overall `-j` job limit.

---

## Phony Targets

A `phony` rule creates an alias that doesn't correspond to a real file on disk — commonly used for convenience targets like `all` or `test`:

```ninja
build all: phony app app_tests
```

Running `ninja all` builds both `app` and `app_tests` without Ninja treating "all" as a file it needs to check for existence or timestamps.

---

## Introspection Tools (`-t`)

Ninja bundles several diagnostic subcommands under `-t`:

```bash
ninja -t targets          # List all known targets
ninja -t targets rule cc  # List targets that use a specific rule
ninja -t query app        # Show what a target depends on, and what depends on it
ninja -t graph app > graph.dot   # Export the dependency graph as Graphviz DOT for visualization
ninja -t commands app     # Print the exact shell commands that would build a target
ninja -t browse           # Launch an interactive web-based dependency graph browser
ninja -t clean            # Equivalent to `ninja clean`, but more configurable
```

`ninja -t browse` in particular is useful for visually understanding why a large project's build graph is shaped the way it is, or for tracking down an unexpectedly slow chain of dependencies.

---

## The `.ninja_log` File

Ninja keeps a log (`.ninja_log`) of how long each build step took on the last run and what command line produced each output. This serves two purposes: it lets `ninja -d stats` report per-step timing for profiling slow builds, and it detects when a command line itself changed (e.g. a compiler flag was added) even if no source file changed — triggering a rebuild of just that affected step.

---

## Ninja vs. Make

| | Ninja | Make |
|---|---|---|
| Meant to be hand-written | No — designed to be generated | Yes, commonly hand-written |
| Built-in logic (conditionals, functions, string ops) | None | Extensive |
| Parse/startup speed on large projects | Very fast | Slower as Makefiles grow complex |
| Implicit rules / pattern matching | None (must be explicit) | Yes (`%.o: %.c` style patterns) |
| Typical role | Backend for CMake/Meson | Standalone or backend for Autotools |

Where a `Makefile` mixes "what to build" with logic like conditionals and functions, a `build.ninja` file only lists build steps and their dependencies — all generated automatically. This trade-off (less flexibility, more speed) is why projects generate Ninja files with CMake or Meson rather than writing them directly.

---

## Who Generates Ninja Files?

- **CMake** — via `-G Ninja`.
- **Meson** — Ninja is Meson's default backend.
- **GN** (Generate Ninja) — Google's own meta-build tool, used by Chromium and V8, purpose-built to emit Ninja files.
- Hand-rolled or custom scripts, for projects willing to write `build.ninja` directly — rare, but sometimes done for very small, performance-sensitive build setups.

---

## Example Walkthrough

```bash
cmake -S . -B build -G Ninja
cd build
ninja
```

Uses CMake to generate a `build.ninja` file, then runs Ninja to compile the project — a common pairing in modern C/C++ projects.

```bash
ninja -j8 -v app
ninja -t graph app > app_graph.dot
```

Builds the `app` target with 8 parallel jobs while printing full commands, then exports its dependency graph for visualization — useful when diagnosing an unexpectedly slow or tangled build.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)