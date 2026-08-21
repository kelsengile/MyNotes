[Previous](./[37]-Logging-and-Diagnostics.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[39]-Code-Signing-and-Notarization.md)

*Distribution*

# Lesson 38 - Building & Packaging for Release

## 38.1 Debug vs Release Builds

A **debug build** includes extra diagnostic information, disables optimizations for faster compiling and clearer stepping, and often keeps development-only checks enabled. A **release build** strips debug symbols (or splits them into a separate symbol file for later crash analysis), enables compiler optimizations, and is what actually ships to users. Always test the release build before shipping — optimization and stripped debug behavior can occasionally surface bugs invisible in debug mode.

---

## 38.2 Bundling Everything the App Needs

Packaging combines your compiled code with everything it needs to run standalone: the runtime (if not assumed to be pre-installed), native dependencies (Lesson 30), assets, and platform manifests. Framework-specific tools automate this: `electron-builder`/`electron-forge` for Electron, `dotnet publish` for .NET, `tauri build` for Tauri, platform-specific `.app` bundling for macOS via Xcode.

```bash
dotnet publish -c Release -r win-x64 --self-contained true
npm run tauri build
```

---

## 38.3 Self-Contained vs Framework-Dependent

A **self-contained** build bundles the runtime itself, producing a larger but more portable output that doesn't require the user to have anything pre-installed. A **framework-dependent** build is smaller but requires the target machine to already have the correct runtime version, which risks a "you must install X to run this" failure for end users unfamiliar with runtimes.

---

## 38.4 Multi-Platform Builds

Producing installers for Windows, macOS, and Linux from one project usually means running the build on (or cross-compiling for) each target OS, often automated in a CI pipeline (Lesson 40) that runs a matrix of builds and uploads the results as release artifacts.

[Previous](./[37]-Logging-and-Diagnostics.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[39]-Code-Signing-and-Notarization.md)
