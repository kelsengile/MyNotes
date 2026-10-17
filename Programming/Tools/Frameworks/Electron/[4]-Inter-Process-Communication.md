[Previous](./[3]-Windows-And-The-App-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[5]-Preload-Scripts-And-Context-Isolation.md)

*Basics*

# Lesson 4 - Inter-Process Communication

## 4.1 ipcMain and ipcRenderer

Main and renderer processes are separate, so they cannot share variables. **Inter-Process Communication (IPC)** is how they exchange messages. Two modules do the work:

- **`ipcMain`** (main process): listens for messages and replies.
- **`ipcRenderer`** (renderer/preload): sends messages to the main process.

Messages travel on named **channels**, which are just strings you choose (for example `'app:get-version'`).

The simplest pattern is one-way: the renderer sends and the main process listens.

```js
// main.js
const { ipcMain } = require('electron')

ipcMain.on('log-message', (event, text) => {
  console.log('Renderer says:', text)
})
```

```js
// preload.js
const { contextBridge, ipcRenderer } = require('electron')

contextBridge.exposeInMainWorld('api', {
  log: (text) => ipcRenderer.send('log-message', text)
})
```

```js
// renderer.js
window.api.log('Hello main process')
```

Notice the renderer never touches `ipcRenderer` directly; the preload exposes a narrow `log` function (more in Lesson 5).

---

## 4.2 Two-way calls with invoke and handle

Most of the time the renderer wants an **answer**. Use `ipcRenderer.invoke` and `ipcMain.handle`, which work like an async function call and return a Promise.

```js
// main.js
const { app, ipcMain, dialog } = require('electron')

ipcMain.handle('app:get-version', () => app.getVersion())

ipcMain.handle('dialog:open-file', async () => {
  const { canceled, filePaths } = await dialog.showOpenDialog({
    properties: ['openFile']
  })
  return canceled ? null : filePaths[0]
})
```

```js
// preload.js
const { contextBridge, ipcRenderer } = require('electron')

contextBridge.exposeInMainWorld('api', {
  getVersion: () => ipcRenderer.invoke('app:get-version'),
  openFile: () => ipcRenderer.invoke('dialog:open-file')
})
```

```js
// renderer.js
const version = await window.api.getVersion()
const file = await window.api.openFile()
```

If the handler throws, the renderer's Promise rejects, so wrap calls in `try/catch`. `invoke`/`handle` is the recommended pattern for request-response communication. Avoid the older `sendSync`, which blocks the renderer until the main process answers.

You can register only one `handle` per channel. Use `ipcMain.removeHandler(channel)` if you need to replace it.

---

## 4.3 Sending messages from main to renderer

The main process can also push messages to a specific window using `webContents.send`:

```js
// main.js
win.webContents.send('update-counter', 1)
```

The renderer must subscribe to the channel through the preload:

```js
// preload.js
const { contextBridge, ipcRenderer } = require('electron')

contextBridge.exposeInMainWorld('api', {
  onUpdateCounter: (callback) => {
    const listener = (_event, value) => callback(value)
    ipcRenderer.on('update-counter', listener)
    // Return a function so the page can unsubscribe
    return () => ipcRenderer.removeListener('update-counter', listener)
  }
})
```

```js
// renderer.js
const stop = window.api.onUpdateCounter((value) => {
  document.getElementById('count').textContent = value
})
// later: stop()
```

Two details worth remembering:

- Pass only the value to the callback, never the raw `event`, so the page cannot reach `event.sender` or other internals.
- Return an unsubscribe function to avoid leaking listeners.

A common real-world use: a menu item in the main process triggers an action in the page.

```js
const menu = Menu.buildFromTemplate([
  {
    label: 'Counter',
    submenu: [
      { label: 'Increment', click: () => win.webContents.send('update-counter', 1) }
    ]
  }
])
Menu.setApplicationMenu(menu)
```

---

## 4.4 Validating IPC messages

Treat every IPC message as **untrusted input**. If a page is compromised, it can send any message on any channel it can reach. Good practice:

**1. Validate the sender.** Make sure the message comes from a frame you expect.

```js
function validateSender(frame) {
  // Only trust your own local page
  return new URL(frame.url).protocol === 'file:'
}

ipcMain.handle('file:read', async (event, name) => {
  if (!validateSender(event.senderFrame)) return null
  // ...
})
```

**2. Validate the arguments.** Check types, ranges, and formats before using them.

```js
ipcMain.handle('notes:save', async (event, title, body) => {
  if (typeof title !== 'string' || title.length === 0 || title.length > 100) {
    throw new Error('Invalid title')
  }
  if (typeof body !== 'string' || body.length > 100_000) {
    throw new Error('Invalid body')
  }
  // safe to proceed
})
```

**3. Never accept raw paths, commands, or code.** Instead of `api.readFile(anyPath)`, expose purpose-specific functions like `api.loadNote(id)` where the main process decides the real location.

```js
const path = require('node:path')
const notesDir = path.join(app.getPath('userData'), 'notes')

ipcMain.handle('notes:load', async (_e, id) => {
  if (!/^[a-z0-9-]+$/i.test(id)) throw new Error('Bad id')
  return fs.promises.readFile(path.join(notesDir, `${id}.txt`), 'utf8')
})
```

**4. Expose the minimum.** Each channel is an entry point into your privileged code, so fewer channels with strict checks means a smaller attack surface.

---

[Previous](./[3]-Windows-And-The-App-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[5]-Preload-Scripts-And-Context-Isolation.md)