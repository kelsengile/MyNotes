[Previous](./[29]-Inter-Process-Communication.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[31]-Plugin-and-Extension-Systems.md)

*System Integration*

# Lesson 30 - Packaging Native Dependencies

## 30.1 What Counts as a Native Dependency

Some app features need code that isn't pure managed/interpreted logic: image/video codecs, hardware access, cryptography libraries, or performance-critical native modules (a C++ addon in Electron, a `.dll`/`.so`/`.dylib` called via FFI). These native dependencies must be bundled and loaded correctly per platform and per CPU architecture.

---

## 30.2 Platform and Architecture Matrices

A native dependency compiled for Windows x64 won't run on macOS ARM64 (Apple Silicon) — each combination of OS and CPU architecture needs its own compiled binary. Modern apps commonly need to support at least: Windows x64, macOS x64 (Intel) and ARM64 (Apple Silicon), and Linux x64. Build pipelines must compile or fetch the correct binary for each target rather than shipping one binary for everyone.

---

## 30.3 Bundling Native Binaries

Frameworks provide a mechanism to include native binaries in the packaged app and load them relative to the install location rather than a hardcoded development path:

```javascript
// Electron: extraResources in electron-builder config bundles native files
// then load at runtime relative to process.resourcesPath
const binPath = path.join(process.resourcesPath, 'bin', 'ffmpeg');
```

Failing to bundle correctly is a common source of "works on my machine, breaks after packaging" bugs, since development often runs against a system-installed dependency the end user won't have.

---

## 30.4 Licensing Native Code

Bundling a native dependency also means bundling its license obligations — some libraries (GPL-family) impose requirements on how they can be redistributed inside a proprietary app. Check the license of any bundled native binary before shipping, not just the license of a source package you compiled it from.

[Previous](./[29]-Inter-Process-Communication.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[31]-Plugin-and-Extension-Systems.md)
