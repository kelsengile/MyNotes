[Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[2]-Development-Environment.md)

*Getting Started*

# Lesson 1 - What is Desktop Development? Native vs Cross-Platform

## 1.1 What Counts as a Desktop App

A desktop application is software that installs and runs directly on a user's operating system (Windows, macOS, or Linux) rather than inside a browser tab or on a remote server. Desktop apps get direct access to the filesystem, native windowing systems, hardware devices, and OS-level services like notifications and the system tray. Examples range from simple utilities and command-line tools to full IDEs, media editors, and office suites.

---

## 1.2 Native Development

"Native" means writing an app with the platform's own toolkit and language: WinForms/WPF or WinUI in C# for Windows, AppKit or SwiftUI in Swift for macOS, GTK or Qt in C/C++ for Linux. Native apps typically offer the best performance, the most authentic look-and-feel, and the deepest access to platform-specific APIs, but the same codebase can't be reused across operating systems — you maintain a separate project per platform.

---

## 1.3 Cross-Platform Development

Cross-platform frameworks let you write one codebase and ship it to multiple operating systems:

- **Electron** — bundles a Chromium browser and Node.js runtime; UI is built with HTML/CSS/JavaScript.
- **Qt** — a C++ framework (with bindings for Python, etc.) that renders native-looking widgets on every platform.
- **.NET MAUI** — Microsoft's evolution of Xamarin.Forms, sharing C#/XAML code across Windows, macOS, and mobile.
- **Tauri** — pairs a Rust backend with a system-provided web view instead of bundling a browser, producing much smaller binaries than Electron.

Cross-platform tools trade some native performance and platform fidelity for drastically reduced development and maintenance effort.

---

## 1.4 Choosing an Approach

The right choice depends on your priorities:

| Priority | Better fit |
|---|---|
| Maximum performance / deepest OS integration | Native |
| Fastest time-to-market across platforms | Cross-platform |
| Small team, web skillset | Electron or Tauri |
| Existing C# codebase | .NET MAUI |
| Small binary size | Tauri or Qt |

This course covers concepts that apply broadly to both approaches, using illustrative examples from several ecosystems.

[Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[2]-Development-Environment.md)
