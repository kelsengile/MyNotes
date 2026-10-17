[Previous](./[2]-Main-And-Renderer-Processes.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[4]-Inter-Process-Communication.md)

*Basics*

# Lesson 3 - Windows And The App Lifecycle

## 3.1 Creating a BrowserWindow

`BrowserWindow` is the class that creates and controls a native window. It can only be used after the app is ready.

```js
const { app, BrowserWindow } = require('electron')
const path = require('node:path')

function createMainWindow() {
  const win = new BrowserWindow({
    width: 1000,
    height: 700,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })

  // Load a local file...
  win.loadFile('index.html')
  // ...or a URL (useful for a dev server)
  // win.loadURL('http://localhost:5173')

  return win
}

app.whenReady().then(createMainWindow)
```

Useful methods on a window instance:

| Method | What it does |
|---|---|
| `win.loadFile(path)` | Loads a local HTML file |
| `win.loadURL(url)` | Loads a web address |
| `win.close()` | Closes the window |
| `win.minimize()` / `win.maximize()` | Changes window state |
| `win.setTitle(title)` | Sets the title bar text |
| `win.webContents` | The object that controls the page inside (DevTools, messaging, navigation) |

---

## 3.2 Window options and appearance

The constructor accepts many options. Common ones:

```js
const win = new BrowserWindow({
  width: 1000,
  height: 700,
  minWidth: 400,
  minHeight: 300,
  show: false,                 // do not show until content is ready
  backgroundColor: '#1e1e1e',  // avoids a white flash on dark apps
  title: 'My App',
  resizable: true,
  alwaysOnTop: false,
  icon: path.join(__dirname, 'icon.png')
})
```

**Avoiding the blank-window flash**: create the window hidden and show it once the first paint is ready.

```js
win.once('ready-to-show', () => {
  win.show()
})
```

**Custom title bars**: `frame: false` removes the native frame entirely, while `titleBarStyle` gives finer control.

```js
const win = new BrowserWindow({
  titleBarStyle: 'hidden',
  // Show native window controls over your own title bar (Windows/Linux)
  titleBarOverlay: { color: '#1e1e1e', symbolColor: '#ffffff', height: 36 }
})
```

With a hidden title bar you must mark a draggable area in CSS, otherwise the window cannot be moved:

```css
.titlebar {
  -webkit-app-region: drag;
}
.titlebar button {
  -webkit-app-region: no-drag;
}
```

---

## 3.3 App lifecycle events

The `app` object emits events that describe the life of your application.

| Event | When it fires |
|---|---|
| `will-finish-launching` | Very early, before ready |
| `ready` (or `app.whenReady()`) | Electron finished initializing; safe to create windows |
| `activate` | macOS: dock icon clicked |
| `window-all-closed` | The last window closed |
| `before-quit` | Before windows start closing |
| `will-quit` | After windows closed, right before quitting |
| `quit` | The app is quitting |

The classic setup handles platform differences. On macOS apps usually keep running with no windows; on Windows and Linux they quit:

```js
app.whenReady().then(() => {
  createMainWindow()

  app.on('activate', () => {
    if (BrowserWindow.getAllWindows().length === 0) createMainWindow()
  })
})

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit()
})
```

**Single instance**: prevent users from launching a second copy and focus the existing one instead.

```js
let mainWindow

const gotLock = app.requestSingleInstanceLock()

if (!gotLock) {
  app.quit()
} else {
  app.on('second-instance', () => {
    if (mainWindow) {
      if (mainWindow.isMinimized()) mainWindow.restore()
      mainWindow.focus()
    }
  })

  app.whenReady().then(() => {
    mainWindow = createMainWindow()
  })
}
```

**Cleanup**: use `will-quit` to release resources such as global shortcuts and database connections.

---

## 3.4 Multiple and child windows

You can create as many windows as you need. Keep references so you can manage them later.

```js
const windows = new Set()

function openWindow() {
  const win = new BrowserWindow({ width: 600, height: 400 })
  win.loadFile('index.html')
  windows.add(win)
  win.on('closed', () => windows.delete(win))
  return win
}
```

**Child windows** stay on top of their parent and close with it. A **modal** child also blocks the parent until it is closed.

```js
const child = new BrowserWindow({
  parent: mainWindow,
  modal: true,
  width: 400,
  height: 300,
  show: false
})
child.loadFile('settings.html')
child.once('ready-to-show', () => child.show())
```

**Windows opened by the page** (for example `window.open()` or links with `target="_blank"`) are controlled with `setWindowOpenHandler`. You can allow them, deny them, or customize them:

```js
mainWindow.webContents.setWindowOpenHandler(({ url }) => {
  if (url === 'https://myapp.example/help') {
    return {
      action: 'allow',
      overrideBrowserWindowOptions: { width: 500, height: 600 }
    }
  }
  return { action: 'deny' }
})
```

Denying by default is a good security habit (see Lesson 11).

---

[Previous](./[2]-Main-And-Renderer-Processes.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[4]-Inter-Process-Communication.md)