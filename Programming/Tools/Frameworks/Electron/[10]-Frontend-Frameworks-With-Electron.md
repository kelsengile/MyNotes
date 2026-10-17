[Previous](./[9]-Data-Storage-And-Settings.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[11]-Security-Best-Practices.md)

*Native Features*

# Lesson 10 - Frontend Frameworks With Electron

## 10.1 Plain HTML, CSS, and JavaScript

The renderer is a web page, so you can build it with nothing but the basics. This is the best way to learn how Electron works and is plenty for small tools.

```
my-app/
├── main.js
├── preload.js
├── index.html
├── styles.css
└── renderer.js
```

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'" />
    <link rel="stylesheet" href="styles.css" />
    <title>Notes</title>
  </head>
  <body>
    <h1>Notes</h1>
    <input id="title" placeholder="Title" />
    <button id="save">Save</button>
    <ul id="list"></ul>
    <script src="renderer.js"></script>
  </body>
</html>
```

```js
// renderer.js
const list = document.getElementById('list')

async function refresh() {
  const notes = await window.api.notes.list()
  list.replaceChildren(
    ...notes.map((n) => {
      const li = document.createElement('li')
      li.textContent = n.title // textContent avoids HTML injection
      return li
    })
  )
}

document.getElementById('save').addEventListener('click', async () => {
  await window.api.notes.add(document.getElementById('title').value, '')
  refresh()
})

refresh()
```

Prefer `textContent` or DOM methods over `innerHTML` with untrusted data, because injected HTML is how XSS attacks start (Lesson 11). Scripts must be loaded from files, not inline, when your Content Security Policy forbids inline code.

---

## 10.2 Vite with React or Vue

For larger apps, a frontend framework and a bundler make development faster. **Vite** is a popular choice, and **Electron Forge** has an official Vite template that wires everything together.

Create a project (TypeScript + Vite):

```bash
npm init electron-app@latest my-app -- --template=vite-typescript
cd my-app
npm install react react-dom
npm install --save-dev @types/react @types/react-dom @vitejs/plugin-react
```

The scaffold gives you separate Vite configs for the main process, the preload, and the renderer (`vite.main.config.ts`, `vite.preload.config.ts`, `vite.renderer.config.ts`). Add the React plugin to the renderer config:

```ts
// vite.renderer.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()]
})
```

A small React entry point:

```tsx
// src/renderer.tsx
import React, { useEffect, useState } from 'react'
import { createRoot } from 'react-dom/client'

function App() {
  const [version, setVersion] = useState('')

  useEffect(() => {
    window.api.getVersion().then(setVersion)
  }, [])

  return <h1>My App v{version}</h1>
}

createRoot(document.getElementById('root')!).render(<App />)
```

In the main process, the Forge Vite plugin provides two constants to load the right page in development and production:

```ts
// src/main.ts
if (MAIN_WINDOW_VITE_DEV_SERVER_URL) {
  mainWindow.loadURL(MAIN_WINDOW_VITE_DEV_SERVER_URL)
} else {
  mainWindow.loadFile(
    path.join(__dirname, `../renderer/${MAIN_WINDOW_VITE_NAME}/index.html`)
  )
}
```

Vue works the same way: install `vue` and `@vitejs/plugin-vue`, and add the plugin to the renderer config. The framework only changes how you build the UI; the process model, preload, and IPC rules stay identical.

---

## 10.3 Development workflow and hot reload

With the Forge Vite template, one command starts everything:

```bash
npm start
```

What you get:

- **Renderer hot module replacement (HMR)**: edit a component and the window updates instantly without losing app state.
- **Main process restarts**: changing main process code restarts the app, because Node code in the main process cannot be hot swapped. You can also type `rs` in the terminal running `npm start` to restart manually.
- **Preload changes** reload the page when rebuilt.

Open DevTools automatically while developing:

```ts
if (!app.isPackaged) {
  mainWindow.webContents.openDevTools({ mode: 'detach' })
}
```

`app.isPackaged` is `false` during development and `true` in a packaged build, so it is the standard switch for dev-only behavior (DevTools, verbose logging, dev server URLs).

Useful habits:

- Keep types for your preload API in one shared file (Lesson 5).
- Install React DevTools or Vue DevTools extensions in development only, using `session.defaultSession.loadExtension(path)` or a helper package.
- Keep business logic that touches the file system or database in the main process, and UI code in the renderer. A good test: if it needs Node, it belongs in main.

---

## 10.4 Remembering window state

Users expect an app to reopen at the same size and position. Save the window bounds when it closes and restore them at startup.

```js
const { app, BrowserWindow, screen } = require('electron')
const path = require('node:path')
const fs = require('node:fs')

const stateFile = path.join(app.getPath('userData'), 'window-state.json')

function loadState() {
  try {
    return JSON.parse(fs.readFileSync(stateFile, 'utf8'))
  } catch {
    return { width: 1000, height: 700 }
  }
}

function isVisibleOnSomeDisplay(bounds) {
  return screen.getAllDisplays().some((d) => {
    const a = d.workArea
    return (
      bounds.x >= a.x &&
      bounds.y >= a.y &&
      bounds.x + bounds.width <= a.x + a.width &&
      bounds.y + bounds.height <= a.y + a.height
    )
  })
}

function createWindow() {
  const state = loadState()
  const hasPosition = state.x !== undefined && state.y !== undefined
  const useBounds = hasPosition && isVisibleOnSomeDisplay(state)

  const win = new BrowserWindow({
    width: state.width,
    height: state.height,
    ...(useBounds ? { x: state.x, y: state.y } : {}),
    show: false
  })

  if (state.isMaximized) win.maximize()
  win.once('ready-to-show', () => win.show())

  win.on('close', () => {
    const isMaximized = win.isMaximized()
    // getNormalBounds returns the un-maximized size and position
    const bounds = win.getNormalBounds()
    fs.writeFileSync(stateFile, JSON.stringify({ ...bounds, isMaximized }))
  })

  win.loadFile('index.html')
  return win
}
```

The `isVisibleOnSomeDisplay` check matters: if the user disconnects a monitor, a window saved on it would otherwise open off-screen. Packages like `electron-window-state` handle these edge cases if you prefer a ready-made solution.

---

[Previous](./[9]-Data-Storage-And-Settings.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[11]-Security-Best-Practices.md)