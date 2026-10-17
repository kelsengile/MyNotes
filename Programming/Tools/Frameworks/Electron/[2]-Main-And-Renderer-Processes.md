[Previous](./[1]-What-Is-Electron.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[3]-Windows-And-The-App-Lifecycle.md)

*Basics*

# Lesson 2 - Main And Renderer Processes

## 2.1 The multi-process architecture

Electron inherits Chromium's multi-process design. Instead of one big process, an app is split into several that each have a job:

- **One main process**: the app's entry point. It runs Node.js, owns the application lifecycle, and creates windows.
- **One renderer process per web content**: each window (or web view) gets its own renderer that draws a web page.
- **Utility processes**: optional child processes started with `utilityProcess` for heavy or untrusted work (for example, parsing large files) without blocking the main process.
- **Helper processes**: Chromium's own GPU and network service processes, which you rarely touch directly.

```
            ┌────────────────────┐
            │    Main process    │  Node.js + Electron APIs
            └─────────┬──────────┘
        creates       │       creates
      ┌───────────────┼───────────────┐
┌─────▼─────┐   ┌─────▼─────┐   ┌─────▼─────┐
│ Renderer  │   │ Renderer  │   │  Utility  │
│ (window 1)│   │ (window 2)│   │  process  │
└───────────┘   └───────────┘   └───────────┘
```

Why split things up? **Stability** (a crashing renderer does not kill the whole app) and **security** (web content can be kept away from powerful system APIs).

---

## 2.2 The main process

The main process runs your `main` file. It has full access to Node.js and to Electron's main-process modules.

Typical responsibilities:

- Creating and managing windows with `BrowserWindow`
- Handling app lifecycle events through `app`
- Building menus, tray icons, and notifications
- Opening native dialogs
- Reading and writing files and databases
- Listening for messages from renderers (IPC)

```js
const { app, BrowserWindow } = require('electron')

app.whenReady().then(() => {
  const win = new BrowserWindow({ width: 900, height: 600 })
  win.loadFile('index.html')
})
```

Because the main process is a single, long-lived process, avoid blocking it. A freeze here freezes every window's native behavior (menus, dialogs, window dragging). Do heavy work asynchronously or move it into a utility process:

```js
const { utilityProcess } = require('electron')
const path = require('node:path')

const child = utilityProcess.fork(path.join(__dirname, 'worker.js'))
child.postMessage({ task: 'crunch', size: 1000000 })
child.on('message', (result) => console.log('worker said', result))
```

---

## 2.3 Renderer processes

A renderer process is essentially a browser tab. It runs your HTML, CSS, and JavaScript and has access to the standard Web APIs: the DOM, `fetch`, `localStorage`, and so on.

By default, modern Electron renderers are **locked down**:

- `nodeIntegration` is `false`, so `require('fs')` is not available in page code.
- `contextIsolation` is `true`, so the page's JavaScript is separated from Electron's internal and preload code.
- `sandbox` is `true` (default since Electron 20), restricting what the process can do at the OS level.

This is intentional. If a renderer loads untrusted content or has a cross-site scripting bug, the attacker should not be able to read your files or run commands.

So how does a page save a file? It cannot do it directly. Instead it **asks the main process** to do it through IPC (Lesson 4), via a safe bridge you define in a preload script (Lesson 5).

A renderer can also be debugged like a web page: open DevTools with `win.webContents.openDevTools()`.

---

## 2.4 Preload scripts overview

A **preload script** is a JavaScript file that runs in the renderer process *before* the web page loads. It sits in between the two worlds:

- It has access to a limited, safe subset of Electron and Node APIs.
- It can expose carefully chosen functions to the page using `contextBridge`.

You attach a preload when creating a window:

```js
const { BrowserWindow } = require('electron')
const path = require('node:path')

const win = new BrowserWindow({
  webPreferences: {
    preload: path.join(__dirname, 'preload.js')
  }
})
```

A minimal preload that exposes version info to the page:

```js
// preload.js
const { contextBridge } = require('electron')

contextBridge.exposeInMainWorld('versions', {
  node: () => process.versions.node,
  chrome: () => process.versions.chrome,
  electron: () => process.versions.electron
})
```

The page can then read them:

```js
// renderer.js
document.title = `Electron ${window.versions.electron()}`
```

Think of the preload as a small, audited **API surface** between your UI and your privileged code. The next lessons show how to build real features on top of it.

---

[Previous](./[1]-What-Is-Electron.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[3]-Windows-And-The-App-Lifecycle.md)# [2]-Main-And-Renderer-Processes

