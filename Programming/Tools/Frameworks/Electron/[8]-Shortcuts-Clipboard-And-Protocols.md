[Previous](./[7]-Dialogs-And-The-File-System.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[9]-Data-Storage-And-Settings.md)

*Native Features*

# Lesson 8 - Shortcuts, Clipboard, And Protocols

## 8.1 Global and local shortcuts

There are two kinds of keyboard shortcuts.

**Local shortcuts** only work while your app is focused. The easiest way is a menu `accelerator` (Lesson 6):

```js
{
  label: 'Find',
  accelerator: 'CmdOrCtrl+F',
  click: () => win.webContents.send('shortcut:find')
}
```

For keys that are not tied to a menu item, listen to input events on the page:

```js
win.webContents.on('before-input-event', (event, input) => {
  if (input.type === 'keyDown' && input.control && input.key.toLowerCase() === 'k') {
    event.preventDefault()
    win.webContents.send('shortcut:command-palette')
  }
})
```

**Global shortcuts** work even when your app is not focused, using `globalShortcut`. Register them after the app is ready and always unregister on quit.

```js
const { app, globalShortcut } = require('electron')

app.whenReady().then(() => {
  const ok = globalShortcut.register('CommandOrControl+Shift+Space', () => {
    if (mainWindow.isVisible()) mainWindow.hide()
    else mainWindow.show()
  })

  if (!ok) console.warn('Global shortcut registration failed')
})

app.on('will-quit', () => {
  globalShortcut.unregisterAll()
})
```

Notes:

- Registration can fail if another app already owns the shortcut. Check the return value.
- Use global shortcuts sparingly; they steal key combinations system-wide.
- On macOS, some shortcuts require accessibility permissions.

---

## 8.2 The clipboard

In the **renderer**, the standard async Clipboard API works in secure contexts and is usually enough:

```js
await navigator.clipboard.writeText('Copied!')
const text = await navigator.clipboard.readText()
```

In the **main process**, Electron's `clipboard` module offers more formats and does not need user interaction:

```js
const { clipboard, nativeImage } = require('electron')

// Text
clipboard.writeText('Hello clipboard')
console.log(clipboard.readText())

// HTML
clipboard.writeHTML('<b>Bold</b> text')

// Images
const image = nativeImage.createFromPath('picture.png')
clipboard.writeImage(image)
const pasted = clipboard.readImage()
console.log(pasted.isEmpty() ? 'No image' : pasted.getSize())

// Several formats at once
clipboard.write({ text: 'Plain', html: '<i>Rich</i>' })
```

To use these from the page, expose narrow functions through the preload:

```js
// preload.js
contextBridge.exposeInMainWorld('api', {
  copyText: (text) => ipcRenderer.invoke('clipboard:write-text', text)
})

// main.js
ipcMain.handle('clipboard:write-text', (_e, text) => {
  if (typeof text !== 'string') throw new Error('Invalid text')
  clipboard.writeText(text)
})
```

Treat clipboard contents as sensitive: users copy passwords and private data. Only read it in response to a clear user action.

---

## 8.3 Custom protocols and deep links

A **custom protocol** such as `myapp://open/note/42` lets links in browsers, emails, or other apps launch your application. This is called a **deep link**.

**Step 1: register your app as the handler.**

```js
const { app } = require('electron')
const path = require('node:path')

if (process.defaultApp) {
  // Running via `electron .` during development
  if (process.argv.length >= 2) {
    app.setAsDefaultProtocolClient('myapp', process.execPath, [
      path.resolve(process.argv[1])
    ])
  }
} else {
  app.setAsDefaultProtocolClient('myapp')
}
```

**Step 2: handle the incoming link.** The mechanism differs by platform.

macOS emits `open-url`:

```js
app.on('open-url', (event, url) => {
  event.preventDefault()
  handleDeepLink(url)
})
```

Windows and Linux start a new instance with the link in the command-line arguments, so use the single-instance lock (Lesson 3) and read it in `second-instance`:

```js
const gotLock = app.requestSingleInstanceLock()
if (!gotLock) {
  app.quit()
} else {
  app.on('second-instance', (_event, argv) => {
    const url = argv.find((arg) => arg.startsWith('myapp://'))
    if (url) handleDeepLink(url)
    if (mainWindow) {
      if (mainWindow.isMinimized()) mainWindow.restore()
      mainWindow.focus()
    }
  })
}
```

**Step 3: parse and validate the link.** Deep links come from outside your app, so they are untrusted input.

```js
function handleDeepLink(rawUrl) {
  let url
  try {
    url = new URL(rawUrl)
  } catch {
    return
  }
  if (url.protocol !== 'myapp:') return

  if (url.hostname === 'open') {
    const id = url.pathname.replace('/note/', '')
    if (/^\d+$/.test(id)) mainWindow.webContents.send('open-note', id)
  }
}
```

Registration only fully takes effect in a **packaged** app on some platforms; the packager configuration (Lesson 14) can declare protocols too.

---

## 8.4 Opening external links safely

Links in your page should open in the user's default browser, not inside your app window. Use `shell.openExternal`, but **never pass it untrusted URLs unchecked**, because it can launch any registered protocol handler (including `file:` or other app schemes).

```js
const { shell } = require('electron')

function openSafely(rawUrl) {
  try {
    const url = new URL(rawUrl)
    if (url.protocol === 'https:' || url.protocol === 'http:') {
      shell.openExternal(url.toString())
    }
  } catch {
    // ignore malformed URLs
  }
}

// Intercept window.open and target="_blank"
win.webContents.setWindowOpenHandler(({ url }) => {
  openSafely(url)
  return { action: 'deny' }
})

// Stop the app window itself from navigating away
win.webContents.on('will-navigate', (event, url) => {
  const appOrigin = 'file://'
  if (!url.startsWith(appOrigin)) {
    event.preventDefault()
    openSafely(url)
  }
})
```

Other `shell` helpers:

```js
shell.showItemInFolder('/path/to/file.txt') // reveal in Finder/Explorer
await shell.openPath('/path/to/file.txt')   // open with the default app
```

Allow-list schemes (`https:`, optionally `mailto:`) rather than block-listing bad ones, and keep the logic in the main process.

---

[Previous](./[7]-Dialogs-And-The-File-System.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[9]-Data-Storage-And-Settings.md)