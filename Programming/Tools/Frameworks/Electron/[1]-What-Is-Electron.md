[Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[2]-Main-And-Renderer-Processes.md)

*Basics*

# Lesson 1 - What Is Electron

## 1.1 Chromium and Node.js together

Electron combines two existing technologies into one runtime:

- **Chromium** is the open-source browser engine behind Google Chrome. It renders HTML, CSS, and JavaScript and gives you the same Web APIs (`fetch`, `localStorage`, WebSockets, Canvas, and so on) you use in a normal browser.
- **Node.js** is a JavaScript runtime that can talk to the operating system: read files, spawn processes, open sockets, and use thousands of npm packages.

Electron glues them together and adds its own APIs (`app`, `BrowserWindow`, `Menu`, `Tray`, `dialog`, and more) for native desktop features. Every Electron app ships its own copy of Chromium and Node.js, so your app behaves identically on every machine regardless of which browser or Node version the user has installed.

You can see the bundled versions from any Electron process:

```js
console.log(process.versions.electron)
console.log(process.versions.chrome)
console.log(process.versions.node)
```

The trade-off of bundling a whole browser is size and memory: a minimal Electron app is roughly 100 MB or more on disk and uses more RAM than a purely native app.

---

## 1.2 Electron vs Tauri and native apps

| Approach | UI technology | Runtime shipped with app | Typical app size | Language for backend |
|---|---|---|---|---|
| **Electron** | HTML, CSS, JavaScript | Chromium + Node.js | 80-200 MB | JavaScript / TypeScript |
| **Tauri** | HTML, CSS, JavaScript | Uses the OS web view | 3-15 MB | Rust |
| **Native (Swift, C#, C++)** | Platform toolkits | None (uses OS libraries) | Small | Platform language |

Key points when choosing:

- **Consistency**: Electron renders the same everywhere because it brings its own browser engine. Tauri depends on the system web view (WebView2, WebKit, WebKitGTK), which can differ between platforms and versions.
- **Team skills**: Electron lets a web team stay in JavaScript end to end. Tauri requires some Rust for native logic.
- **Performance and footprint**: native apps and Tauri are lighter. Electron trades footprint for a single, predictable runtime and a huge ecosystem.
- **Maturity**: Electron powers many widely used editors, chat clients, and design tools and has a large community and documentation base.

---

## 1.3 Installing and your first app

You need Node.js (an LTS version) and npm installed. Create a project and install Electron as a development dependency:

```bash
mkdir my-first-app
cd my-first-app
npm init -y
npm install --save-dev electron
```

Edit `package.json` so it names the entry file and adds a start script:

```json
{
  "name": "my-first-app",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": {
    "start": "electron ."
  }
}
```

Create `main.js`. This is the **main process** entry point:

```js
const { app, BrowserWindow } = require('electron')

function createWindow() {
  const win = new BrowserWindow({
    width: 800,
    height: 600
  })
  win.loadFile('index.html')
}

app.whenReady().then(() => {
  createWindow()

  // macOS: re-create a window when the dock icon is clicked and none are open
  app.on('activate', () => {
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

// Windows and Linux: quit when all windows are closed
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit()
})
```

Create `index.html`, the page shown inside the window:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta
      http-equiv="Content-Security-Policy"
      content="default-src 'self'"
    />
    <title>Hello Electron</title>
  </head>
  <body>
    <h1>Hello from Electron!</h1>
  </body>
</html>
```

Run it:

```bash
npm start
```

A desktop window should open showing your page. To skip manual setup, Electron Forge can scaffold a complete project:

```bash
npm init electron-app@latest my-app
```

---

## 1.4 Project layout

A small hand-made project usually looks like this:

```
my-first-app/
├── package.json     # name, version, "main" entry, scripts
├── main.js          # main process: creates windows, handles app events
├── preload.js       # preload script: safe bridge to the renderer
├── index.html       # the page loaded in the window
├── renderer.js      # code that runs inside the page
└── node_modules/
```

What each file does:

- **`package.json`**: the `main` field tells Electron which file to run first. Without it Electron looks for `index.js`.
- **`main.js`**: runs in the main process with full Node.js access. It controls the app lifecycle and creates windows.
- **`preload.js`**: runs before the page loads, with a limited bridge to Node and Electron APIs (see Lesson 5).
- **`index.html` / `renderer.js`**: the user interface, which runs in a renderer process like a web page.

Larger projects created with Electron Forge add folders such as `src/` and a `forge.config.js` file, and often split code into `main`, `preload`, and `renderer` directories. The idea stays the same: privileged code on one side, UI code on the other.

---

[Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[2]-Main-And-Renderer-Processes.md)