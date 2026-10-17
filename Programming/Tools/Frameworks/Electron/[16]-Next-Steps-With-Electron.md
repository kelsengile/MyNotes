[Previous](./[15]-Auto-Updates-And-Releases.md) | [Table of Contents](./[0]-Introduction-to-Electron.md)

*Hardening And Delivery*

# Lesson 16 - Next Steps With Electron

## 16.1 TypeScript with Electron

TypeScript adds static types to JavaScript and catches many mistakes before the app runs. It is especially valuable in Electron because messages cross process boundaries, where a typo in a channel name or argument is easy to make.

The easiest start is a Forge template with TypeScript:

```bash
npm init electron-app@latest my-app -- --template=vite-typescript
```

Or add it to an existing project:

```bash
npm install --save-dev typescript
npx tsc --init
```

A reasonable `tsconfig.json` core:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "outDir": "dist"
  },
  "include": ["src"]
}
```

Electron ships its own type definitions, so `import { app, BrowserWindow } from 'electron'` is fully typed with no extra package.

Types really shine for **IPC**. Define channels once and share them between main, preload, and renderer:

```ts
// src/shared/ipc.ts
export interface IpcChannels {
  'app:get-version': { args: []; result: string }
  'notes:save': { args: [id: string, text: string]; result: void }
}

export type Channel = keyof IpcChannels
```

```ts
// src/main/ipc.ts
import { ipcMain } from 'electron'
import type { IpcChannels, Channel } from '../shared/ipc'

export function handle<C extends Channel>(
  channel: C,
  fn: (...args: IpcChannels[C]['args']) => Promise<IpcChannels[C]['result']> | IpcChannels[C]['result']
) {
  ipcMain.handle(channel, (_event, ...args) => fn(...(args as IpcChannels[C]['args'])))
}
```

Remember that types disappear at runtime, so keep validating inputs in the main process (Lesson 4). Typing the preload API is covered in Lesson 5.

---

## 16.2 Native modules

A **native module** is a Node.js add-on written in C or C++ (or Rust) that compiles to a `.node` file. You use one when you need speed or access to system libraries that JavaScript cannot reach, such as `better-sqlite3` (Lesson 9) or serial port and USB libraries.

The challenge: Electron bundles its own Node.js build with a different **ABI** (binary interface) than the Node.js on your computer. A module compiled for regular Node.js will fail to load in Electron with an error like "was compiled against a different Node.js version".

The fix is to **rebuild** the module for Electron:

```bash
npm install --save-dev @electron/rebuild
npx electron-rebuild
```

Electron Forge and electron-builder do this automatically during packaging. If you hit build errors, you usually need build tools installed:

- Windows: Visual Studio Build Tools (C++ workload) and Python
- macOS: Xcode Command Line Tools (`xcode-select --install`)
- Linux: `build-essential` and Python

Tips:

- Prefer modules that ship **prebuilt binaries** for Electron (many use `prebuildify` or Node-API), so users and CI skip compilation.
- **Node-API (N-API)** modules are ABI stable across Node versions, so they break less often when you upgrade Electron.
- Unpack `.node` files from ASAR (Lesson 14).
- Load native modules only in the **main process** or a utility process, not the sandboxed renderer.
- If you write your own, `node-addon-api` (C++) and `napi-rs` (Rust) are good starting points.
- Every native module adds a maintenance burden per platform and architecture (x64 and arm64), so choose pure-JavaScript or WebAssembly alternatives when they are fast enough.

---

## 16.3 Alternatives like Tauri

Electron is not the only way to build desktop apps with web technology. Knowing the alternatives helps you choose the right tool for each project.

| Tool | Approach | Strengths | Trade-offs |
|---|---|---|---|
| **Electron** | Ships Chromium + Node.js | Same rendering everywhere, huge ecosystem, all-JavaScript stack | Large size, higher memory |
| **Tauri** | Rust backend + OS web view | Tiny binaries, low memory, strong security model | Needs Rust for native logic; web view differs per OS |
| **Neutralino** | Lightweight runtime + OS web view | Very small, simple | Smaller ecosystem and fewer native features |
| **Flutter / .NET MAUI / Qt** | Own UI toolkit | Native look and performance, no browser engine | Different languages and UI model |
| **Progressive Web App (PWA)** | Installable website | No packaging, instant updates | Limited OS access |

How to decide:

- Pick **Electron** when you want consistent behavior, rich Node.js libraries, a JavaScript-only team, and mature tooling (updates, crash reporting, signing).
- Pick **Tauri** when installer size and memory matter most and your team is comfortable with Rust.
- Pick a **native toolkit** when you need the deepest platform integration and best performance.
- Pick a **PWA** when the user only needs a web app that can be installed, with no system access.

Many skills transfer between all of them: web UI development, IPC design, least privilege, and release engineering.

---

## 16.4 Project ideas

The best way to learn Electron is to build something. Ideas ordered from easier to harder:

1. **Markdown notes app.** Open and save files with dialogs (Lesson 7), live preview, and a menu with shortcuts (Lesson 6).
2. **Pomodoro timer in the tray.** Tray icon with a countdown, notifications when sessions end (Lesson 6), and saved settings (Lesson 9).
3. **Clipboard history manager.** Poll the clipboard in the main process, store entries in SQLite, and open with a global shortcut (Lessons 8 and 9).
4. **Image batch resizer.** Drag and drop a folder of images (Lesson 7), process them in a utility process so the UI stays responsive (Lesson 2), and show progress through IPC (Lesson 4).
5. **Personal finance tracker.** SQLite database with migrations, charts in a React renderer (Lessons 9 and 10), and encrypted export with `safeStorage` (Lesson 9).
6. **Local-first to-do app with sync.** Offline database, a sync server, deep links to open tasks (Lesson 8), and auto-updates (Lesson 15).
7. **Mini code editor.** Embed Monaco or CodeMirror, open folders, remember window state (Lesson 10), and write E2E tests with Playwright (Lesson 13).
8. **Screen annotation overlay.** A transparent, always-on-top window toggled by a global shortcut (Lessons 3 and 8).

For each project, practice the full cycle:

- Design a **small, safe preload API** (Lesson 5).
- Apply the **security checklist** (Lesson 11).
- Add **tests** and **CI** (Lesson 13).
- **Package, sign, and publish** a release with auto-updates (Lessons 14 and 15).

Good places to keep learning: the official Electron documentation and its tutorial, the Electron Forge documentation, and open-source Electron apps whose code you can read to see how larger projects are organized.

---

[Previous](./[15]-Auto-Updates-And-Releases.md) | [Table of Contents](./[0]-Introduction-to-Electron.md)