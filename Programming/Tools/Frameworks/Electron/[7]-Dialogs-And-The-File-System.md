[Previous](./[6]-Menus-Tray-And-Notifications.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[8]-Shortcuts-Clipboard-And-Protocols.md)

*Native Features*

# Lesson 7 - Dialogs And The File System

## 7.1 Open and save dialogs

The `dialog` module shows native file pickers and message boxes. It runs in the **main process**, so the renderer asks for it through IPC.

**Open dialog** (returns a Promise):

```js
const { dialog, ipcMain } = require('electron')

ipcMain.handle('file:open', async () => {
  const { canceled, filePaths } = await dialog.showOpenDialog({
    title: 'Open a note',
    properties: ['openFile'],
    filters: [
      { name: 'Text files', extensions: ['txt', 'md'] },
      { name: 'All files', extensions: ['*'] }
    ]
  })
  return canceled ? null : filePaths[0]
})
```

`properties` can include `openFile`, `openDirectory`, `multiSelections`, and `createDirectory`.

**Save dialog**:

```js
ipcMain.handle('file:save-as', async (_e, suggestedName) => {
  const { canceled, filePath } = await dialog.showSaveDialog({
    defaultPath: suggestedName,
    filters: [{ name: 'Markdown', extensions: ['md'] }]
  })
  return canceled ? null : filePath
})
```

**Message boxes** confirm actions or report problems:

```js
const { response } = await dialog.showMessageBox(win, {
  type: 'warning',
  buttons: ['Delete', 'Cancel'],
  defaultId: 1,
  cancelId: 1,
  message: 'Delete this note?',
  detail: 'This cannot be undone.'
})
if (response === 0) {
  // user chose Delete
}
```

Pass the parent window as the first argument to make the dialog modal to it. `dialog.showErrorBox(title, content)` shows a simple error and can be used even before the app is ready.

---

## 7.2 Reading and writing files

File access belongs in the **main process**, where Node.js is fully available. Use the promise-based `fs/promises` API so you do not block the app.

```js
const fs = require('node:fs/promises')
const { ipcMain, dialog } = require('electron')

ipcMain.handle('note:open', async () => {
  const { canceled, filePaths } = await dialog.showOpenDialog({
    properties: ['openFile']
  })
  if (canceled) return null

  const filePath = filePaths[0]
  const text = await fs.readFile(filePath, 'utf8')
  return { filePath, text }
})

ipcMain.handle('note:save', async (_e, filePath, text) => {
  if (typeof filePath !== 'string' || typeof text !== 'string') {
    throw new Error('Invalid arguments')
  }
  await fs.writeFile(filePath, text, 'utf8')
  return true
})
```

Expose it safely through the preload:

```js
contextBridge.exposeInMainWorld('api', {
  openNote: () => ipcRenderer.invoke('note:open'),
  saveNote: (filePath, text) => ipcRenderer.invoke('note:save', filePath, text)
})
```

Security reminders:

- Do not give the page a generic "read any path" or "write any path" function. Prefer flows where the **user picks the file in a dialog** and the main process only touches what was chosen.
- Always handle errors (missing files, permission denied) and report them to the UI.
- For large files, use streams (`fs.createReadStream`) instead of loading everything into memory.

---

## 7.3 App data and special paths

`app.getPath(name)` returns the right folder for each platform so you never hard-code paths.

| Name | Meaning |
|---|---|
| `userData` | Your app's private data folder (settings, databases, caches) |
| `appData` | The per-user application data directory (parent of `userData`) |
| `documents` | The user's Documents folder |
| `downloads` | The Downloads folder |
| `desktop` | The Desktop folder |
| `home` | The user's home directory |
| `temp` | The temporary files folder |
| `logs` | Where your app's log files should go |

```js
const { app } = require('electron')
const path = require('node:path')
const fs = require('node:fs/promises')

const dataDir = path.join(app.getPath('userData'), 'notes')

async function ensureDataDir() {
  await fs.mkdir(dataDir, { recursive: true })
}
```

Typical `userData` locations:

- Windows: `%APPDATA%\<app name>`
- macOS: `~/Library/Application Support/<app name>`
- Linux: `~/.config/<app name>`

Never store user data inside your app's install folder: it may be read-only and is replaced on every update. Use `userData` for anything that must persist.

---

## 7.4 Drag and drop

**Dropping files into your window** works with standard HTML events. Since Electron 32, the old `file.path` property is gone, so use `webUtils.getPathForFile` from the preload to get a real path.

```js
// preload.js
const { contextBridge, webUtils } = require('electron')

contextBridge.exposeInMainWorld('api', {
  getPathForFile: (file) => webUtils.getPathForFile(file)
})
```

```js
// renderer.js
const dropZone = document.getElementById('drop-zone')

dropZone.addEventListener('dragover', (e) => e.preventDefault())

dropZone.addEventListener('drop', (e) => {
  e.preventDefault()
  for (const file of e.dataTransfer.files) {
    const fullPath = window.api.getPathForFile(file)
    console.log('Dropped:', file.name, fullPath)
  }
})
```

If you only need the file contents, you can often read them in the page with `file.text()` or `file.arrayBuffer()` and skip the path entirely.

**Dragging files out of your app** to the desktop or other apps uses `webContents.startDrag` in the main process:

```js
// main.js
ipcMain.on('drag:start', (event, fileName) => {
  event.sender.startDrag({
    file: path.join(dataDir, fileName),
    icon: path.join(__dirname, 'drag-icon.png')
  })
})
```

```js
// renderer.js: trigger from a dragstart event
item.addEventListener('dragstart', (e) => {
  e.preventDefault()
  window.api.startDrag('note1.txt')
})
```

Always validate file names in the main process (see Lesson 4) so a page cannot request files outside your data folder.

---

[Previous](./[6]-Menus-Tray-And-Notifications.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[8]-Shortcuts-Clipboard-And-Protocols.md)