[Previous](./[10]-Frontend-Frameworks-With-Electron.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[12]-Debugging-And-Performance.md)

*Hardening And Delivery*

# Lesson 11 - Security Best Practices

## 11.1 The Electron security checklist

An Electron app runs with the **full permissions of the user**. A bug that lets an attacker run code in your app can become access to the user's files. Security is therefore a core part of the design, not a final polish step.

The official checklist boils down to these rules:

1. **Only load secure content.** Use `https://`, never `http://`, for remote resources.
2. **Do not enable Node.js integration** for pages that load remote content (`nodeIntegration: false`, the default).
3. **Keep context isolation enabled** (`contextIsolation: true`, the default).
4. **Keep the sandbox enabled** (`sandbox: true`, the default).
5. **Handle session permission requests** from remote content (camera, microphone, geolocation, notifications).
6. **Do not disable `webSecurity`.** It enforces the same-origin policy.
7. **Define a Content Security Policy** and avoid `unsafe-eval`.
8. **Do not set `allowRunningInsecureContent`.**
9. **Do not enable experimental features** or `enableBlinkFeatures` without a strong reason.
10. **Avoid `allowpopups`** on `<webview>`, and verify or avoid `<webview>` entirely.
11. **Limit navigation** and window creation.
12. **Validate the `sender` of every IPC message** and its arguments.
13. **Never call `shell.openExternal` with untrusted URLs.**
14. **Do not use `file://` for remote or user-supplied content**; for your own app pages consider a custom protocol.
15. **Keep Electron updated** to get Chromium and Node.js security fixes.

A hardened window looks like this:

```js
const win = new BrowserWindow({
  webPreferences: {
    preload: path.join(__dirname, 'preload.js'),
    contextIsolation: true,
    nodeIntegration: false,
    sandbox: true,
    webSecurity: true,
    allowRunningInsecureContent: false
  }
})
```

Most of these are already the default; the point is to **never turn them off** to make something easier.

---

## 11.2 Content Security Policy

A **Content Security Policy (CSP)** tells the page which sources of scripts, styles, images, and connections are allowed. Even if an attacker injects HTML, the browser refuses to run script from disallowed places. It is a strong second line of defense against XSS.

**Option 1: a meta tag** in your HTML:

```html
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self';
           script-src 'self';
           style-src 'self' 'unsafe-inline';
           img-src 'self' data:;
           connect-src 'self' https://api.example.com;
           object-src 'none';
           base-uri 'none';
           frame-ancestors 'none'"
/>
```

**Option 2: set it as a response header** for all pages, which also covers pages you load from files:

```js
const { session } = require('electron')

app.whenReady().then(() => {
  session.defaultSession.webRequest.onHeadersReceived((details, callback) => {
    callback({
      responseHeaders: {
        ...details.responseHeaders,
        'Content-Security-Policy': [
          "default-src 'self'; script-src 'self'; object-src 'none'"
        ]
      }
    })
  })
})
```

Guidelines:

- Start strict (`default-src 'self'`) and loosen only what you need.
- Avoid `'unsafe-eval'` and `'unsafe-inline'` for scripts. Move inline scripts into files.
- Dev servers like Vite may need extra allowances (such as a WebSocket in `connect-src`) in development only. Apply the strict policy in production builds.
- Electron prints a security warning in the DevTools console in development when your CSP is missing or weak. Treat those warnings as a to-do list.

---

## 11.3 Navigation and permission control

**Limit navigation.** Your window should never navigate to unexpected sites. If it does, those sites inherit your preload bridge.

```js
win.webContents.on('will-navigate', (event, url) => {
  const allowed = new URL(url).origin === 'https://app.example.com'
  if (!allowed) event.preventDefault()
})

win.webContents.setWindowOpenHandler(({ url }) => {
  // Open external pages in the browser, never in a new app window
  if (url.startsWith('https://')) shell.openExternal(url)
  return { action: 'deny' }
})
```

**Control permissions.** By default Electron may grant permission requests (camera, microphone, geolocation, and more) from web content. Explicitly decide which ones to allow:

```js
const { session } = require('electron')

session.defaultSession.setPermissionRequestHandler((webContents, permission, callback) => {
  const url = webContents.getURL()
  const trusted = url.startsWith('file://') || url.startsWith('https://app.example.com')

  const allowedPermissions = ['clipboard-sanitized-write', 'notifications']
  callback(trusted && allowedPermissions.includes(permission))
})
```

**Restrict `<webview>`** if you use it:

```js
app.on('web-contents-created', (_event, contents) => {
  contents.on('will-attach-webview', (event, webPreferences, params) => {
    delete webPreferences.preload
    webPreferences.nodeIntegration = false
    if (!params.src.startsWith('https://trusted.example.com/')) {
      event.preventDefault()
    }
  })
})
```

Applying these handlers in `web-contents-created` covers every window your app ever creates.

---

## 11.4 Electron Fuses and hardening

**Fuses** are switches flipped at **package time** that disable risky Electron features inside the binary itself. Because they are baked in, an attacker cannot re-enable them by editing files or using environment variables.

Common fuses to consider:

| Fuse | Recommended setting | Why |
|---|---|---|
| `RunAsNode` | Disabled | Stops the binary from being used as a general Node.js runtime via `ELECTRON_RUN_AS_NODE` |
| `EnableNodeOptionsEnvironmentVariable` | Disabled | Ignores `NODE_OPTIONS` |
| `EnableNodeCliInspectArguments` | Disabled | Blocks `--inspect` style debugging flags in production |
| `EnableEmbeddedAsarIntegrityValidation` | Enabled | Detects tampering with the ASAR archive |
| `OnlyLoadAppFromAsar` | Enabled | Only loads app code from the ASAR archive |
| `EnableCookieEncryption` | Enabled | Encrypts cookies on disk |

With Electron Forge, use the Fuses plugin:

```bash
npm install --save-dev @electron-forge/plugin-fuses @electron/fuses
```

```js
// forge.config.js
const { FusesPlugin } = require('@electron-forge/plugin-fuses')
const { FuseV1Options, FuseVersion } = require('@electron/fuses')

module.exports = {
  plugins: [
    new FusesPlugin({
      version: FuseVersion.V1,
      [FuseV1Options.RunAsNode]: false,
      [FuseV1Options.EnableCookieEncryption]: true,
      [FuseV1Options.EnableNodeOptionsEnvironmentVariable]: false,
      [FuseV1Options.EnableNodeCliInspectArguments]: false,
      [FuseV1Options.EnableEmbeddedAsarIntegrityValidation]: true,
      [FuseV1Options.OnlyLoadAppFromAsar]: true
    })
  ]
}
```

Without Forge, use `flipFuses` from `@electron/fuses` on the packaged executable.

Additional hardening habits:

- Run `npm audit` and keep dependencies updated; supply-chain issues are a common attack route.
- Sign your app (Lesson 14) so users and operating systems can trust it.
- Never ship secrets (API keys) inside the app; anything in the bundle can be extracted.
- Test your security settings, and review them whenever you upgrade Electron, since defaults and options evolve.

---

[Previous](./[10]-Frontend-Frameworks-With-Electron.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[12]-Debugging-And-Performance.md)