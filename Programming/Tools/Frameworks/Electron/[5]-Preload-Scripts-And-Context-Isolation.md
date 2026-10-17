[Previous](./[4]-Inter-Process-Communication.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[6]-Menus-Tray-And-Notifications.md)

*Basics*

# Lesson 5 - Preload Scripts And Context Isolation

## 5.1 Why context isolation exists

A renderer runs two kinds of JavaScript: **your preload script** (trusted, with access to Electron APIs) and **the web page** (potentially untrusted, especially if it loads remote content or has an XSS bug).

Without isolation, both would share the same JavaScript world. A malicious page could then:

- Overwrite built-in objects (like `Array.prototype`) that your preload relies on
- Reach `ipcRenderer` or Node modules the preload imported
- Escalate from "can run script in a page" to "can run code on the user's machine"

**Context isolation** puts the preload script and Electron internals in a **separate JavaScript context** from the page. The two worlds share the same DOM but have separate globals. Anything crossing between them must go through `contextBridge`.

```
┌──────────────────────────┐   contextBridge   ┌───────────────────────┐
│ Isolated world (preload) │  ───────────────► │ Main world (the page) │
│ ipcRenderer, Node bits   │   only copies of  │ window.api (safe)     │
└──────────────────────────┘   exposed values  └───────────────────────┘
```

It is enabled by default in modern Electron. Keep it that way:

```js
new BrowserWindow({
  webPreferences: {
    contextIsolation: true,   // default
    nodeIntegration: false,   // default
    sandbox: true,            // default
    preload: path.join(__dirname, 'preload.js')
  }
})
```

---

## 5.2 contextBridge

`contextBridge.exposeInMainWorld(name, api)` creates `window[name]` in the page, containing the functions and values you pass in.

```js
// preload.js
const { contextBridge, ipcRenderer } = require('electron')

contextBridge.exposeInMainWorld('api', {
  platform: process.platform,                       // a plain value
  ping: () => ipcRenderer.invoke('ping'),           // a function
  onMessage: (cb) => {
    const listener = (_e, msg) => cb(msg)
    ipcRenderer.on('message', listener)
    return () => ipcRenderer.removeListener('message', listener)
  }
})
```

How values cross the bridge:

- Primitives, plain objects, arrays, Promises, and functions are supported. They are **copied**, not shared by reference.
- Functions are proxied: the page calls a proxy, which runs your function in the isolated world.
- Things that cannot be cloned safely (DOM nodes, class instances with prototypes, `Error` details beyond basics) do not pass through unchanged.

You can only expose to the main world with `exposeInMainWorld`. If you set `window.api = ...` directly in the preload, the page will not see it when isolation is on.

---

## 5.3 Designing a safe API surface

The bridge is your app's **security boundary**. A few rules keep it strong.

**Never expose raw Electron modules.** This hands the page the whole IPC system:

```js
// BAD: the page can send any message on any channel
contextBridge.exposeInMainWorld('electron', { ipcRenderer })

// BAD: lets the page pick any channel name
contextBridge.exposeInMainWorld('api', {
  send: (channel, ...args) => ipcRenderer.send(channel, ...args)
})
```

**Expose one function per purpose.** Each wraps a fixed channel:

```js
// GOOD
contextBridge.exposeInMainWorld('api', {
  getVersion: () => ipcRenderer.invoke('app:get-version'),
  saveNote: (id, text) => ipcRenderer.invoke('notes:save', id, text),
  onThemeChange: (cb) => {
    const listener = (_e, theme) => cb(theme)
    ipcRenderer.on('theme:changed', listener)
    return () => ipcRenderer.removeListener('theme:changed', listener)
  }
})
```

**Group related functions with namespaces** to keep large APIs readable:

```js
contextBridge.exposeInMainWorld('api', {
  notes: {
    list: () => ipcRenderer.invoke('notes:list'),
    save: (id, text) => ipcRenderer.invoke('notes:save', id, text)
  },
  window: {
    minimize: () => ipcRenderer.send('window:minimize')
  }
})
```

**Do not pass the IPC `event` object** to callbacks, and **always validate again in the main process** (Lesson 4). The preload is a convenience layer, not a replacement for checks on the privileged side.

**Keep it small.** The fewer functions you expose, the fewer ways they can be abused.

---

## 5.4 Typing the preload API with TypeScript

In TypeScript, the page does not know that `window.api` exists. Describe it once in a shared declaration so autocomplete and type checking work in your renderer code.

Define the API shape:

```ts
// src/shared/api.d.ts
export interface NotesApi {
  list(): Promise<string[]>
  save(id: string, text: string): Promise<void>
}

export interface AppApi {
  getVersion(): Promise<string>
  notes: NotesApi
  onThemeChange(cb: (theme: 'light' | 'dark') => void): () => void
}

declare global {
  interface Window {
    api: AppApi
  }
}
```

Implement it in the preload, letting TypeScript check it matches:

```ts
// src/preload.ts
import { contextBridge, ipcRenderer } from 'electron'
import type { AppApi } from './shared/api'

const api: AppApi = {
  getVersion: () => ipcRenderer.invoke('app:get-version'),
  notes: {
    list: () => ipcRenderer.invoke('notes:list'),
    save: (id, text) => ipcRenderer.invoke('notes:save', id, text)
  },
  onThemeChange: (cb) => {
    const listener = (_e: unknown, theme: 'light' | 'dark') => cb(theme)
    ipcRenderer.on('theme:changed', listener)
    return () => ipcRenderer.removeListener('theme:changed', listener)
  }
}

contextBridge.exposeInMainWorld('api', api)
```

Use it in the renderer with full type support:

```ts
const version: string = await window.api.getVersion()
window.api.notes.save('todo', 'buy milk')
```

Make sure your `tsconfig.json` includes the declaration file so the global `Window` augmentation is picked up. Types are only a development aid: they are erased at runtime, so validation in the main process is still required.

---

[Previous](./[4]-Inter-Process-Communication.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[6]-Menus-Tray-And-Notifications.md)