[Previous](./[26]-Linux-Desktop-Development.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[28]-Working-with-the-OS.md)

*Platform-Specific & Cross-Platform Frameworks*

# Lesson 27 - Cross-Platform Frameworks (Electron, Qt, .NET MAUI, Tauri)

## 27.1 Electron

Electron bundles Chromium (rendering) and Node.js (system access) into every app, so UI is built entirely with web technology (HTML/CSS/JS or a framework like React). This gives web developers a very low barrier to entry and access to the huge npm ecosystem, at the cost of large binary sizes (each app ships its own browser) and higher memory use.

```javascript
const { app, BrowserWindow } = require('electron');
app.whenReady().then(() => {
  const win = new BrowserWindow({ width: 800, height: 600 });
  win.loadFile('index.html');
});
```

---

## 27.2 Tauri

Tauri takes a similar web-UI approach but uses the OS's built-in web view (WebView2 on Windows, WebKit on macOS/Linux) instead of bundling Chromium, and a Rust backend instead of Node. This produces dramatically smaller binaries and lower memory overhead than Electron, at the cost of possible rendering inconsistencies across the different underlying web view engines.

---

## 27.3 Qt

Qt (C++, with Python/Rust/other bindings) renders its own widgets rather than relying on a web view, aiming for native-looking output on every platform from one codebase. It's a mature, deeply featured framework used in many commercial and embedded products, but has a steeper learning curve and (for the commercial license) licensing costs for closed-source apps.

---

## 27.4 .NET MAUI

.NET MAUI (Multi-platform App UI) lets C#/XAML code target Windows, macOS, iOS, and Android from one project, rendering through each platform's native controls rather than a custom widget set or web view. It's the natural choice for teams already invested in the .NET ecosystem who want to extend beyond Windows-only WPF/WinForms apps.

---

## 27.5 Comparing the Options

| Framework | UI approach | Language | Binary size |
|---|---|---|---|
| Electron | Bundled Chromium | JS/HTML/CSS | Large |
| Tauri | System web view | Rust + JS/HTML/CSS | Small |
| Qt | Custom-rendered widgets | C++ (+ bindings) | Medium |
| .NET MAUI | Native platform controls | C#/XAML | Medium |

[Previous](./[26]-Linux-Desktop-Development.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[28]-Working-with-the-OS.md)
