[Previous](./[5]-Preload-Scripts-And-Context-Isolation.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[7]-Dialogs-And-The-File-System.md)

*Native Features*

# Lesson 6 - Menus, Tray, And Notifications

## 6.1 Application menus

The application menu is the menu bar (top of the screen on macOS, top of the window on Windows and Linux). You build it from a template array and install it with `Menu.setApplicationMenu`.

```js
const { app, Menu, BrowserWindow } = require('electron')

const isMac = process.platform === 'darwin'

const template = [
  // macOS puts the app's own menu first
  ...(isMac ? [{ role: 'appMenu' }] : []),
  {
    label: 'File',
    submenu: [
      {
        label: 'New Note',
        accelerator: 'CmdOrCtrl+N',
        click: () => {
          const win = BrowserWindow.getFocusedWindow()
          if (win) win.webContents.send('menu:new-note')
        }
      },
      { type: 'separator' },
      isMac ? { role: 'close' } : { role: 'quit' }
    ]
  },
  { role: 'editMenu' },
  { role: 'viewMenu' },
  { role: 'windowMenu' }
]

app.whenReady().then(() => {
  Menu.setApplicationMenu(Menu.buildFromTemplate(template))
})
```

Key concepts:

- **`role`**: a built-in behavior (`copy`, `paste`, `undo`, `quit`, `reload`, `toggleDevTools`) that is automatically localized and wired up. Prefer roles over writing your own handlers.
- **`accelerator`**: a keyboard shortcut. `CmdOrCtrl` means Command on macOS and Control elsewhere.
- **`click`**: your own handler, called in the main process. To affect the page, send an IPC message.
- **`type`**: `normal`, `separator`, `checkbox`, `radio`, or `submenu`.
- Setting `Menu.setApplicationMenu(null)` removes the default menu on Windows and Linux.

Without an `editMenu`, standard shortcuts like Ctrl/Cmd+C and V may not work on macOS, so keep it in the template.

---

## 6.2 Context menus

A context menu appears on right-click. The page can ask the main process to show one, or the main process can listen for the `context-menu` event on the page.

```js
const { Menu } = require('electron')

win.webContents.on('context-menu', (_event, params) => {
  const items = []

  if (params.isEditable) {
    items.push({ role: 'cut' }, { role: 'copy' }, { role: 'paste' })
  } else if (params.selectionText) {
    items.push({ role: 'copy' })
  }

  if (items.length > 0) {
    Menu.buildFromTemplate(items).popup({ window: win })
  }
})
```

To drive the menu from your own UI, expose a function through the preload and handle it in the main process:

```js
// preload.js
contextBridge.exposeInMainWorld('api', {
  showItemMenu: (itemId) => ipcRenderer.send('menu:show-item', itemId)
})

// main.js
ipcMain.on('menu:show-item', (event, itemId) => {
  const menu = Menu.buildFromTemplate([
    { label: 'Rename', click: () => event.sender.send('item:rename', itemId) },
    { label: 'Delete', click: () => event.sender.send('item:delete', itemId) }
  ])
  menu.popup({ window: BrowserWindow.fromWebContents(event.sender) })
})
```

---

## 6.3 Tray icons

A tray icon lives in the system tray (Windows and Linux) or the menu bar (macOS). It is handy for background apps.

```js
const { app, Tray, Menu, nativeImage } = require('electron')
const path = require('node:path')

let tray = null // keep a reference, or it may be garbage collected

app.whenReady().then(() => {
  const icon = nativeImage.createFromPath(path.join(__dirname, 'trayTemplate.png'))
  tray = new Tray(icon)
  tray.setToolTip('My App')

  tray.setContextMenu(
    Menu.buildFromTemplate([
      { label: 'Show', click: () => mainWindow.show() },
      { label: 'Quit', click: () => app.quit() }
    ])
  )

  tray.on('click', () => mainWindow.show())
})
```

Tips:

- Keep the `tray` variable in module scope. If it goes out of scope, the icon can disappear.
- On macOS, name template images with a `Template` suffix (for example `trayTemplate.png`) so they adapt to light and dark menu bars. Use small images, typically 16x16 with an `@2x` version.
- A common pattern is "close to tray": intercept the window `close` event and hide instead.

```js
let isQuitting = false
app.on('before-quit', () => { isQuitting = true })

mainWindow.on('close', (event) => {
  if (!isQuitting) {
    event.preventDefault()
    mainWindow.hide()
  }
})
```

---

## 6.4 Notifications

There are two ways to show a desktop notification.

**From the renderer**, using the standard Web Notifications API:

```js
new Notification('Download complete', { body: 'report.pdf is ready' })
```

**From the main process**, using Electron's `Notification` class:

```js
const { Notification } = require('electron')

function notify(title, body) {
  if (!Notification.isSupported()) return

  const n = new Notification({ title, body, silent: false })
  n.on('click', () => {
    mainWindow.show()
    mainWindow.focus()
  })
  n.show()
}
```

Platform notes:

- **Windows**: set an App User Model ID so notifications are attributed to your app: `app.setAppUserModelId('com.example.myapp')`. In development, notifications may show under Electron's identity until the app is packaged.
- **macOS**: the user must allow notifications for your app in System Settings. Notifications for unsigned or unpackaged apps may behave differently during development.
- **Linux**: notifications rely on a notification daemon, which most desktops include.

Handle clicks to bring the user back to the relevant part of your app, and avoid spamming: notifications are an interruption, so send them only for events the user cares about.

---

[Previous](./[5]-Preload-Scripts-And-Context-Isolation.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[7]-Dialogs-And-The-File-System.md)