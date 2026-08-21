[Previous](./[2]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[4]-Command-Line-Interfaces-and-Console-Apps.md)

*Getting Started*

# Lesson 3 - Anatomy of a Desktop App Project

## 3.1 Typical Folder Layout

Most desktop app project templates share a similar shape, regardless of framework:

```
my-app/
├── src/            # application source code
├── assets/         # icons, images, fonts
├── resources/      # platform manifests, entitlements, .plist/.rc files
├── build/ or dist/ # compiled output (usually git-ignored)
├── tests/          # unit and integration tests
├── package.json / .csproj / Cargo.toml   # project & dependency manifest
└── README.md
```

The manifest file (`.csproj` for .NET, `package.json` for Electron, `Cargo.toml` for Tauri) declares the app's name, version, dependencies, and build targets.

---

## 3.2 The Entry Point

Every desktop app has a single entry point where the OS hands control to your code — `Main()` in C#/C++, `main.js`/`main.ts` in Electron's main process, or `fn main()` in Rust. This entry point typically:

1. Initializes the application/runtime.
2. Creates the main window.
3. Registers global handlers (menus, tray icon, IPC channels).
4. Starts the event loop (covered in Lesson 15).

---

## 3.3 Resources and Assets

Icons, fonts, and images are bundled into the app rather than fetched over a network, so they must be referenced with paths that remain valid after packaging (not just during local development). Frameworks provide a resource-embedding mechanism — .NET's embedded resources, Qt's `.qrc` files, Electron's `extraResources` — specifically so assets survive the transition from source tree to installed binary.

---

## 3.4 Configuration Files

Alongside code, a project usually carries platform manifests that describe metadata the OS needs: a Windows `.rc`/manifest file for icons and version info, a macOS `Info.plist` for bundle identifiers and permissions, and a Linux `.desktop` file for menu integration. These are covered in more depth in Lessons 24–27 and 38.

[Previous](./[2]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[4]-Command-Line-Interfaces-and-Console-Apps.md)
