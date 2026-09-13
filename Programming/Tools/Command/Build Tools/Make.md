[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Make

Make is one of the oldest and most widely used build automation tools. It reads a `Makefile` describing how to turn source files into a finished program, and only rebuilds the parts that actually changed.

Download: [https://www.gnu.org/software/make/](https://www.gnu.org/software/make/)

---

## What Is Make?

A `Makefile` defines **targets** (things you want to build), the **prerequisites** each target depends on, and the **recipe** (shell commands) used to build it. Make compares file timestamps: if a prerequisite is newer than its target, the recipe runs again; otherwise Make skips it, which is what makes large rebuilds fast. This timestamp-based dependency graph is Make's core idea, and nearly every build tool that came after it (Ninja, Bazel, MSBuild) is solving the same problem in a different way.

---

## A Minimal Makefile

```make
program: main.o utils.o
	gcc -o program main.o utils.o

main.o: main.c
	gcc -c main.c

utils.o: utils.c
	gcc -c utils.c

clean:
	rm -f *.o program
```

Recipe lines must start with a literal Tab character, not spaces — this trips up almost everyone the first time.

---

## Core Commands

```bash
make                # Build the first target in the Makefile
make target_name     # Build a specific target
make clean           # Common convention for removing build output
make -j4             # Build using 4 parallel jobs
make -n              # Dry run — print commands without running them
```

---

## Variables

```make
CC = gcc
CFLAGS = -Wall -O2
OBJS = main.o utils.o

program: $(OBJS)
	$(CC) $(CFLAGS) -o program $(OBJS)
```

Variables (referenced with `$(VAR)`) let a Makefile centralize things like the compiler name or flags, so switching compilers or adding a flag means editing one line instead of every recipe. Make also has automatic variables inside recipes: `$@` (the target's name), `$<` (the first prerequisite), and `$^` (all prerequisites), which keep pattern rules generic.

---

## Pattern Rules

```make
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@
```

A pattern rule like this replaces writing a separate rule for every `.c` file — Make matches any `.o` target against a same-named `.c` file automatically, which is how real-world Makefiles avoid repeating the same recipe dozens of times.

---

## Phony Targets

```make
.PHONY: clean test

clean:
	rm -f *.o program

test:
	./run_tests.sh
```

`clean` and `test` don't produce files matching their own names, so if a file named `clean` ever existed in the directory, Make would think the target is already "up to date" and skip it. Declaring them `.PHONY` tells Make these targets should always run regardless of any file with that name.

---

## Conditionals and Includes

```make
ifeq ($(OS),Windows_NT)
	RM = del
else
	RM = rm -f
endif

include config.mk
```

Conditionals let a single Makefile adapt to different platforms, and `include` lets large projects split configuration into separate files that get pulled into the main Makefile.

---

## Why It Still Matters

Even projects that use modern build systems like CMake or Meson often generate a Makefile as their final output, since `make`'s dependency-tracking model is simple, fast, and available on virtually every Unix-like system by default.

---

## Common Gotchas

- Spaces vs tabs: the single most common Makefile error is a recipe line indented with spaces instead of a Tab, producing a `missing separator` error.
- Stale targets: if a target's prerequisite doesn't actually change the target's content (e.g. touching a file without editing it), Make will still rebuild — Make trusts timestamps, not content hashes.

---

## Example Walkthrough

```bash
make
make clean
make -j4
```

Builds the project using the default target, removes the build output, then rebuilds it again using 4 parallel jobs for a faster compile.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Build Tools/CMake.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# CMake

CMake is a cross-platform build system **generator** — rather than compiling code itself, it reads a `CMakeLists.txt` file and generates native build files (Makefiles, Ninja files, Visual Studio projects, Xcode projects) for whatever platform you're on.

Download: [https://cmake.org/](https://cmake.org/)

---

## What Is CMake?

Before CMake, cross-platform C/C++ projects often needed separate, hand-maintained build configurations for Linux, macOS, and Windows. CMake solves that by letting a project describe its structure once, in a platform-independent `CMakeLists.txt`, and generate whichever native build tooling is appropriate for the machine actually building it — Unix Makefiles on Linux, Ninja files anywhere, or a Visual Studio solution on Windows.

---

## The Two-Stage Workflow

```bash
mkdir build && cd build
cmake ..                 # Configure: reads CMakeLists.txt, generates build files
cmake --build .           # Build: invokes the underlying build tool (make, ninja, etc.)
```

The "configure, then build" split is central to CMake: the configure step figures out compilers, libraries, and options and writes them into the `build/` directory; the build step actually compiles. Keeping `build/` separate from the source tree (an "out-of-source build") is the standard convention, since it lets you delete `build/` entirely to start clean without touching any source files.

---

## A Minimal CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyApp)

add_executable(myapp main.cpp utils.cpp)
```

`add_executable` declares a target and the source files that build it — CMake works out the compiler invocations and link step for the current platform automatically.

---

## Core Commands

```bash
cmake -S . -B build                    # Configure explicitly with source (-S) and build (-B) dirs
cmake --build build                     # Build using whatever generator was configured
cmake --build build --target clean      # Run a specific target, e.g. clean
cmake --build build -j8                 # Build with 8 parallel jobs
cmake -DCMAKE_BUILD_TYPE=Release ..     # Set a build-time variable (Release vs Debug)
cmake --install build                   # Install the built artifacts
ccmake ..                               # Interactive terminal UI for configuring options
```

---

## Build Types and Variables

```bash
cmake -DCMAKE_BUILD_TYPE=Debug ..
cmake -DCMAKE_BUILD_TYPE=Release ..
```

`CMAKE_BUILD_TYPE` controls optimization and debug-symbol flags: `Debug` adds symbols and disables optimization for easier debugging, while `Release` optimizes for speed. Custom variables can be defined with `set()` inside `CMakeLists.txt`, or overridden from the command line with `-D`.

---

## Finding Dependencies

```cmake
find_package(OpenSSL REQUIRED)
target_link_libraries(myapp PRIVATE OpenSSL::SSL)
```

`find_package` is how CMake locates external libraries already installed on the system, using either CMake's own bundled search scripts or config files the library itself provides. `target_link_libraries` then wires the found library into a specific target's link step.

---

## Choosing a Generator

```bash
cmake -G "Ninja" ..                  # Generate Ninja build files instead of Makefiles
cmake -G "Unix Makefiles" ..         # Explicit Makefile generator
cmake -G "Visual Studio 17 2022" ..  # Generate a Visual Studio solution
```

CMake picks a sensible default generator per platform, but `-G` overrides it — Ninja is a popular choice because its build files are optimized for very fast incremental rebuilds compared to traditional Makefiles.

---

## Common Gotchas

- Stale cache: changing a CMake variable sometimes requires deleting `CMakeCache.txt` (or the whole `build/` directory) for the change to take effect cleanly.
- In-source builds: running `cmake .` directly in the source directory scatters generated files throughout the project — almost always avoided in favor of a dedicated `build/` folder.

---

## Example Walkthrough

```bash
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake --build . -j4
```

Configures an out-of-source build in Release mode, then compiles it using 4 parallel jobs — a typical CMake workflow for a C++ project.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Build Tools/Ninja.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Ninja

Ninja is a small, extremely fast build system designed to be a compilation **target** for higher-level tools like CMake or Meson, rather than something developers usually write by hand.

Download: [https://ninja-build.org/](https://ninja-build.org/)

---

## What Is Ninja?

Ninja intentionally has almost no built-in logic for things like conditionals, string manipulation, or pattern-matching — those decisions are made ahead of time by a generator (CMake, Meson, GN) that writes out a plain, explicit `build.ninja` file listing every command needed. Because Ninja's input is already fully expanded and simple, Ninja itself can spend all its effort on scheduling and running builds as fast as possible, which is why incremental rebuilds with Ninja are often noticeably faster than with Make on large projects.

---

## Core Commands

```bash
ninja                     # Build the default target(s) using build.ninja in the current dir
ninja target_name          # Build a specific target
ninja -j8                  # Use 8 parallel jobs (default is based on CPU count)
ninja -n                   # Dry run — show what would be built
ninja -v                   # Verbose — print full commands, not just short status lines
ninja -t targets           # List all known targets
ninja -t clean             # Clean build outputs
ninja -t graph | dot -Tpng -o graph.png  # Visualize the dependency graph
```

---

## A Minimal build.ninja

```ninja
rule cc
  command = gcc -c $in -o $out

build main.o: cc main.c
build program: cc main.o
```

Ninja files use `rule` blocks to define a reusable command template (with `$in`/`$out` placeholders) and `build` statements to say which rule produces which output from which input — deliberately close to raw shell invocations, with almost no abstraction layered on top.

---

## Why It's Usually Generated, Not Hand-Written

Because `build.ninja` files list every single compile and link command explicitly with no loops or conditionals, hand-maintaining one for a real project would mean manually tracking every source file. In practice, developers write a CMakeLists.txt or meson.build once, and let CMake or Meson regenerate the Ninja file automatically whenever the project's file list or settings change.

---

## Performance Features

- **Parallelism by default** — Ninja runs jobs in parallel across all CPU cores without extra flags, unlike Make which needs `-j` to enable it.
- **Minimal startup overhead** — Ninja's file format is designed to be parsed extremely quickly, which matters on projects with tens of thousands of build steps.
- **Precise dependency tracking** — Ninja supports compiler-generated dependency files (`.d` files) natively, so it rebuilds exactly the files affected by a header change, not more.

---

## Common Gotchas

- Regenerating stale build files: if the generator (e.g. CMake) isn't re-run after adding new source files, Ninja has no way to know about them, since it only reads the static file it's given.
- Debugging failures: because Ninja's default output is terse status lines, `-v` is usually the first thing to add when a build fails and you need to see the actual failing command.

---

## Example Walkthrough

```bash
cmake -G Ninja -B build .
ninja -C build -j8
```

Has CMake generate Ninja build files, then runs Ninja against that build directory with 8 parallel jobs — the typical way most developers actually interact with Ninja.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Build Tools/Meson.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Meson

Meson is a modern, high-level build system that emphasizes speed and ease of use, generating Ninja files (by default) to actually perform the build.

Download: [https://mesonbuild.com/](https://mesonbuild.com/)

---

## What Is Meson?

Meson was created partly as a reaction to the complexity of tools like Autotools and raw CMake: its configuration language (`meson.build`) is intentionally simple and non-Turing-complete, which keeps build definitions easy to read and analyze. Like CMake, Meson is a generator rather than a build executor — it almost always outputs Ninja files, then delegates the actual compiling to Ninja.

---

## The Two-Stage Workflow

```bash
meson setup builddir          # Configure: reads meson.build, generates a Ninja build in builddir/
meson compile -C builddir     # Build: runs Ninja under the hood
```

Like CMake, Meson strictly separates configuration from building and always uses an out-of-source build directory — there's no supported way to build directly inside the source tree.

---

## A Minimal meson.build

```meson
project('myapp', 'cpp')
executable('myapp', 'main.cpp', 'utils.cpp')
```

`executable()` declares a build target directly, with a much shorter syntax than the equivalent CMakeLists.txt for the same result — this terseness is one of Meson's main selling points.

---

## Core Commands

```bash
meson setup builddir                     # Create and configure a build directory
meson setup --reconfigure builddir       # Re-run configuration after changing options
meson compile -C builddir                # Build the project
meson test -C builddir                   # Run the project's test suite
meson install -C builddir                # Install built artifacts
meson configure builddir                 # View or change build options
meson setup -Dbuildtype=release builddir # Set a build type option
```

---

## Build Options and Types

```bash
meson setup -Dbuildtype=release builddir
meson setup -Doptimization=3 -Ddebug=false builddir
```

Meson defines its own set of standard options (`buildtype`, `optimization`, `debug`, `warning_level`) that map to sensible compiler flags automatically, so switching from a debug build to a release build doesn't require remembering raw compiler flags.

---

## Dependencies

```meson
zlib_dep = dependency('zlib')
executable('myapp', 'main.cpp', dependencies: zlib_dep)
```

`dependency()` looks up a library via `pkg-config`, CMake config files, or Meson's own wrap system, and the result is passed straight into a target — a more declarative style than CMake's separate `find_package` + `target_link_libraries` steps.

---

## The WrapDB Dependency System

```bash
meson wrap install zlib
```

Meson's "wrap" system can fetch and build a missing dependency's source code automatically if it isn't found on the system, using recipes from the community-maintained WrapDB — useful for projects that need to work reliably across machines without every dependency pre-installed.

---

## Common Gotchas

- Immutable build directories: `meson.build` changes are usually picked up automatically by `meson compile`, but changing something Meson can't detect (like an environment variable) may require `meson setup --reconfigure`.
- Ninja is doing the actual work: build failures often show raw compiler errors from underneath Ninja, so understanding that Meson → Ninja → compiler is the real chain helps when debugging.

---

## Example Walkthrough

```bash
meson setup builddir -Dbuildtype=release
meson compile -C builddir
meson test -C builddir
```

Configures a release build, compiles it, then runs the project's test suite — a complete Meson workflow from source to verified build.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)