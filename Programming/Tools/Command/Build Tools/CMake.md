[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# CMake

CMake is a cross-platform **build system generator** — it doesn't compile your code itself, but reads a `CMakeLists.txt` file and generates the native build files (Makefiles, Ninja files, Visual Studio projects, Xcode projects, and more) for whatever platform you're on.

Download: [https://cmake.org/download/](https://cmake.org/download/)

---

## What Is CMake?

CMake was created in 2000 by Kitware, originally to support cross-platform builds for the Insight Toolkit (ITK) medical imaging library. Writing a Makefile by hand that works identically on Linux, macOS, and Windows is painful — different compilers, different linkers, different path conventions. CMake solves this by letting you describe your project once, in a declarative language, then generating the right build files for each platform's native toolchain.

Most modern C and C++ projects use CMake for exactly this reason, and it has become the de facto standard build configuration tool in the C/C++ ecosystem — many IDEs (CLion, Visual Studio, VS Code, Qt Creator) can open a `CMakeLists.txt` directly without any project-file conversion.

---

## A Minimal CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyApp)
add_executable(my_app main.cpp)
```

---

## The Two-Step Model: Configure, Then Build

CMake always works in two distinct phases:

1. **Configure** — CMake reads `CMakeLists.txt`, detects the compiler and platform, resolves dependencies, and writes out native build files plus a `CMakeCache.txt` recording all the choices made.
2. **Generate/Build** — the underlying tool (Make, Ninja, MSBuild, Xcode) actually compiles the code using those generated files.

```bash
cmake -S . -B build          # Configure: read CMakeLists.txt, generate build files in ./build
cmake --build build          # Build the project using the generated build files
cmake --build build --target clean   # Clean the build
cmake --build build --parallel 8     # Build using 8 parallel jobs
ctest --test-dir build       # Run tests defined with CMake's testing support
```

`-S` points to the source directory, `-B` to the (usually separate) build directory — keeping build output out of your source tree is standard practice, often called an "out-of-source build," and makes it trivial to wipe a broken build (`rm -rf build`) without touching source code.

---

## Modern (Target-Based) CMake

Older CMake code set global variables (`include_directories`, `CMAKE_CXX_FLAGS`) that leaked across the whole project. Modern CMake (3.x+) is **target-based**: properties are attached to individual targets and propagate only to things that link against them.

```cmake
add_library(mathutils STATIC mathutils.cpp)
target_include_directories(mathutils PUBLIC include/)
target_compile_features(mathutils PUBLIC cxx_std_17)

add_executable(my_app main.cpp)
target_link_libraries(my_app PRIVATE mathutils)
```

- `PRIVATE` — the setting applies only to this target.
- `PUBLIC` — applies to this target *and* anything that links against it.
- `INTERFACE` — applies only to things that link against it, not the target itself (common for header-only libraries).

---

## Finding and Using Dependencies

```cmake
find_package(ZLIB REQUIRED)
target_link_libraries(my_app PRIVATE ZLIB::ZLIB)
```

`find_package` locates an already-installed library via CMake "config" files or bundled "Find modules" (`FindZLIB.cmake`, etc.). For dependencies not installed system-wide, CMake also offers:

- **FetchContent** — downloads and builds a dependency's source directly as part of the configure step, without needing it pre-installed.
- **ExternalProject** — a more heavyweight alternative that builds a dependency as a separate step/project, useful when it needs its own configure/build/install cycle isolated from the main build.
- **CPM.cmake** — a popular third-party wrapper around FetchContent for simpler dependency declarations.

```cmake
include(FetchContent)
FetchContent_Declare(
  fmt
  GIT_REPOSITORY https://github.com/fmtlib/fmt.git
  GIT_TAG 10.1.1
)
FetchContent_MakeAvailable(fmt)
target_link_libraries(my_app PRIVATE fmt::fmt)
```

---

## Build Types (Debug, Release, etc.)

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug     # Include debug symbols, no optimization
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release   # Optimized, no debug symbols
cmake -S . -B build -DCMAKE_BUILD_TYPE=RelWithDebInfo  # Optimized but with debug symbols
```

Multi-config generators (Visual Studio, Xcode) ignore `CMAKE_BUILD_TYPE` at configure time and instead choose the configuration at build time: `cmake --build build --config Release`.

---

## Generators

By default CMake picks a sensible generator for your platform (Makefiles on Linux, Visual Studio on Windows), but you can request a specific one:

```bash
cmake -S . -B build -G "Ninja"
cmake -S . -B build -G "Unix Makefiles"
cmake -S . -B build -G "Visual Studio 17 2022"
cmake -S . -B build -G "Xcode"
cmake --help                # Lists all generators available on this machine
```

Ninja is the most common non-default choice, since it builds noticeably faster than Make on large projects (see the Ninja lesson).

---

## Toolchain Files and Cross-Compiling

A **toolchain file** tells CMake to use a different compiler and system root than the host machine — the standard way to cross-compile (e.g. building for ARM/embedded targets from an x86 desktop, or targeting Android/iOS):

```bash
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE=arm-toolchain.cmake
```

```cmake
# arm-toolchain.cmake
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)
set(CMAKE_C_COMPILER arm-linux-gnueabihf-gcc)
set(CMAKE_CXX_COMPILER arm-linux-gnueabihf-g++)
```

---

## CMakePresets.json

Newer CMake versions (3.19+) support **presets** — a JSON file that captures common configure/build/test invocations so contributors don't need to remember long `-D` flag combinations:

```json
{
  "version": 3,
  "configurePresets": [
    {
      "name": "release",
      "binaryDir": "build/release",
      "cacheVariables": { "CMAKE_BUILD_TYPE": "Release" }
    }
  ]
}
```

```bash
cmake --preset release
cmake --build --preset release
```

---

## Installing and Packaging

```cmake
install(TARGETS my_app DESTINATION bin)
install(FILES README.md DESTINATION share/doc/my_app)
```

```bash
cmake --install build --prefix /usr/local
```

For distributing binaries, CMake bundles **CPack**, which can generate `.zip`, `.tar.gz`, `.deb`, `.rpm`, and platform installers directly from the same project definition used to build it.

---

## Testing with CTest

CMake ships with **CTest**, a test-running and reporting tool that integrates with test frameworks like GoogleTest, Catch2, or plain custom scripts:

```cmake
enable_testing()
add_test(NAME MathTests COMMAND test_mathutils)
```

```bash
ctest --test-dir build --output-on-failure
```

---

## The CMake Cache

`CMakeCache.txt`, written into the build directory during configure, stores every option CMake resolved (compiler paths, found libraries, user-set variables) so subsequent builds don't need to re-detect everything. `cmake -LAH build` lists all cached variables with descriptions — useful for discovering configurable options in an unfamiliar project. Deleting the build directory (or just `CMakeCache.txt`) forces a fully fresh configure.

---

## Example Walkthrough

```bash
cmake -S . -B build
cmake --build build
./build/my_app
```

Configures the project into a `build` folder, compiles it using the generated build system, then runs the resulting binary.

```bash
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release
cmake --build build
cmake --install build --prefix ./dist
```

Configures an optimized Release build using Ninja, compiles it, then installs the resulting binaries into a local `dist/` folder.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)