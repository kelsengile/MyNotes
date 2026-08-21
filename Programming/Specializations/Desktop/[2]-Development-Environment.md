[Previous](./[1]-What-is-Desktop-Development.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[3]-Anatomy-of-a-Desktop-App-Project.md)

*Getting Started*

# Lesson 2 - Development Environment & Toolchains

## 2.1 Editors and IDEs

Most desktop developers use an IDE suited to their platform: Visual Studio or VS Code for .NET/Windows work, Xcode for macOS/iOS, and JetBrains IDEs or VS Code for cross-platform and Qt projects. An IDE typically bundles a code editor, debugger, and project/build management in one place, which matters more for desktop development than for scripting because of native compilation and packaging steps.

---

## 2.2 Compilers, SDKs, and Runtimes

A **toolchain** is the set of tools that turns source code into a runnable app: a compiler or transpiler, a linker, an SDK providing platform headers/libraries, and (for managed languages) a runtime like the .NET CLR or a JavaScript engine. Setting up a desktop project usually means installing:

- The language's SDK (e.g. .NET SDK, Xcode Command Line Tools, a C++ compiler like MSVC/Clang/GCC).
- The framework's CLI or project templates (e.g. `dotnet new`, `cargo tauri init`, `electron-forge`).
- Any native build dependencies the framework needs (e.g. Qt's `qmake`/`CMake`, GTK's `pkg-config`).

---

## 2.3 Package Managers

Desktop projects pull in dependencies through a package manager tied to their language: NuGet for .NET, npm/yarn for Electron/Node-based UIs, Cargo for Rust/Tauri, pip for Python/Qt bindings. Lockfiles (`package-lock.json`, `Cargo.lock`, etc.) pin exact dependency versions so builds are reproducible across machines — this matters even more for desktop apps than web apps, since users run the exact binary you shipped rather than fetching fresh assets from a server.

---

## 2.4 Verifying Your Setup

Before starting a project, confirm your toolchain is installed correctly, for example:

```bash
dotnet --version
node --version && npm --version
cargo --version
qmake --version
```

A working "hello world" build is the fastest sanity check — if the framework's starter template compiles and launches a blank window, your environment is ready for real development.

[Previous](./[1]-What-is-Desktop-Development.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[3]-Anatomy-of-a-Desktop-App-Project.md)
