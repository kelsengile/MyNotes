[Previous](./[11]-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[13]-Testing-Electron-Applications.md)

*Hardening And Delivery*

# Lesson 12 - Debugging And Performance

## 12.1 DevTools and debugging the main process

Because two kinds of process exist, there are two kinds of debugging.

**Renderer: Chrome DevTools.** Open them from code or with a shortcut:

```js
win.webContents.openDevTools({ mode: 'detach' })
```

The `viewMenu` role in your menu also includes `toggleDevTools` (Ctrl+Shift+I on Windows/Linux, Cmd+Option+I on macOS). In DevTools you get the Elements, Console, Network, Sources (breakpoints), Performance, and Memory panels, exactly like a browser.

**Main process: the Node.js inspector.** Start Electron with an inspect flag:

```bash
electron --inspect=5858 .
# or pause on the first line:
electron --inspect-brk=5858 .
```

Then attach a debugger: open `chrome://inspect` in Chrome and click your target, or use VS Code. A VS Code launch configuration (`.vscode/launch.json`):

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug Main Process",
      "type": "node",
      "request": "launch",
      "cwd": "${workspaceFolder}",
      "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron",
      "windows": {
        "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron.cmd"
      },
      "args": ["."],
      "outputCapture": "std"
    }
  ]
}
```

Now breakpoints in `main.js` work like in any Node program. Remember that `console.log` in the main process prints to the **terminal**, while `console.log` in the renderer prints to **DevTools**. Many "my log does not appear" problems come from looking in the wrong place.

---

## 12.2 Logging

`console.log` is fine for development, but a shipped app needs **persistent logs** so users can send you a file when something goes wrong.

`electron-log` is a popular helper that writes to a file in the right location for each platform and also captures renderer logs:

```bash
npm install electron-log
```

```js
// main.js
const log = require('electron-log/main')

log.initialize()                 // lets renderers log through the main process
log.transports.file.level = 'info'
log.info('App starting', { version: app.getVersion() })

process.on('uncaughtException', (err) => log.error('Uncaught', err))
process.on('unhandledRejection', (reason) => log.error('Unhandled rejection', reason))
```

Log files go under `app.getPath('logs')`, which is a good place to point users to when they report bugs. You can also add a menu item that reveals the folder:

```js
{
  label: 'Open Log Folder',
  click: () => shell.openPath(app.getPath('logs'))
}
```

Logging tips:

- Use levels (`error`, `warn`, `info`, `debug`) and lower the verbosity in production.
- **Never log secrets**, tokens, or personal data.
- Rotate or cap file size so logs do not grow forever (most logging libraries do this by default).
- Listen for failures that do not throw into your own code, such as `render-process-gone` and `child-process-gone` on `app`.

---

## 12.3 Startup time and memory use

Electron apps have a reputation for being heavy. Most of that is avoidable.

**Startup time**

- **Defer work.** Do the minimum before showing the window, then load heavier things afterwards.
- **Show the window when ready**: create it with `show: false` and use `ready-to-show` so users see a painted window, not a blank one.
- **Lazy-load modules** that are not needed immediately:

```js
ipcMain.handle('export:pdf', async (_e, data) => {
  const { generatePdf } = await import('./pdf-exporter.js') // loaded on first use
  return generatePdf(data)
})
```

- **Bundle your code** (Vite, esbuild, webpack) so Node does not load hundreds of small files from disk.
- Avoid synchronous file or network calls in the main process during startup.
- Measure first: log timestamps (`performance.now()`) at key points and use DevTools' Performance tab for the renderer.

**Memory use**

- Every window is a full Chromium renderer. Create fewer windows, and close hidden ones you no longer need.
- Use `app.getAppMetrics()` and `process.getProcessMemoryInfo()` to see how much each process uses.
- In DevTools' **Memory** panel take heap snapshots to find leaks. Typical leak sources are event listeners never removed (see unsubscribe functions in Lesson 4), timers that are never cleared, and large arrays held forever.
- Render large lists with **virtualization** (only draw visible rows) instead of thousands of DOM nodes.
- Move CPU-heavy work into a `utilityProcess` or a Web Worker so the UI stays smooth.
- Choose dependencies carefully: a small utility is cheaper than a giant library.

---

## 12.4 Crash reporting

Crashes happen on machines you cannot see. Crash reporting sends you diagnostic data so you can fix them.

Electron's built-in `crashReporter` collects **native crash dumps** (minidumps) and uploads them to a server you control:

```js
const { app, crashReporter } = require('electron')

crashReporter.start({
  productName: 'My App',
  companyName: 'Example Inc',
  submitURL: 'https://crashes.example.com/submit',
  uploadToServer: true,
  compress: true
})
```

Start it as early as possible, ideally before the app is ready, so early crashes are captured. You need a server that accepts minidumps (such as a self-hosted Crashpad-compatible service or a hosted product).

Many teams use a hosted service such as **Sentry**, whose Electron SDK captures both native crashes and JavaScript errors in the main and renderer processes with source maps.

Also handle crashes gracefully inside the app:

```js
win.webContents.on('render-process-gone', (_event, details) => {
  log.error('Renderer gone', details.reason)
  if (details.reason !== 'clean-exit') {
    win.reload() // or show an error page with a "Reload" button
  }
})

app.on('child-process-gone', (_event, details) => {
  log.error('Child process gone', details.type, details.reason)
})
```

Privacy matters: tell users what is collected, avoid including personal data, and offer an opt-out where required by law or good practice.

---

[Previous](./[11]-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[13]-Testing-Electron-Applications.md)