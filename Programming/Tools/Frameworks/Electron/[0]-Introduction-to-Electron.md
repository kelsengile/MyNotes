[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to JavaScript](/Programming/Syntax/JavaScript/[0]-Introduction-to-JavaScript.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/electron/electron-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Electron</h1>
</div>


Electron is an open-source framework for building cross-platform desktop applications with JavaScript, HTML, and CSS. It bundles the Chromium rendering engine and the Node.js runtime into a single package, so one web codebase can run as a native app on Windows, macOS, and Linux.

Unlike a website, an Electron app can reach the operating system: it can open native windows, read and write files, show menus and notifications, and register shortcuts. This Topic covers the process model, windows, inter-process communication, native features, data storage, security, testing, packaging, and auto-updates.

Download: [electronjs.org](https://www.electronjs.org/)

### Why Study Electron?

- **One codebase, three platforms** — ship the same app to Windows, macOS, and Linux without learning three native toolkits.
- **Reuse your web skills** — HTML, CSS, JavaScript, and frontend frameworks like React or Vue work as they are.
- **The process model teaches real architecture** — separating a privileged main process from sandboxed renderers is a lesson in least privilege and message passing.
- **Security is part of the craft** — desktop apps run with the user's permissions, so learning to isolate and lock them down is essential.
- **Proven in production** — editors, chat clients, and design tools used by millions are built with Electron.

### Electron At A Glance

| Attribute | Details |
|---|---|
| Language | JavaScript / TypeScript |
| Type | Desktop application framework |
| Built on | Chromium and Node.js |
| Runs on | Windows, macOS, Linux |
| Best for | Cross-platform desktop apps built with web technologies |
| Pairs well with | Electron Forge, Vite, React, Vue, Playwright |

---

### Table of Contents

**Basics**

   1. **[What Is Electron](./[1]-What-Is-Electron.md)**  
       1.1 Chromium and Node.js together  
       1.2 Electron vs Tauri and native apps  
       1.3 Installing and your first app  
       1.4 Project layout  
   2. **[Main And Renderer Processes](./[2]-Main-And-Renderer-Processes.md)**  
       2.1 The multi-process architecture  
       2.2 The main process  
       2.3 Renderer processes  
       2.4 Preload scripts overview  
   3. **[Windows And The App Lifecycle](./[3]-Windows-And-The-App-Lifecycle.md)**  
       3.1 Creating a BrowserWindow  
       3.2 Window options and appearance  
       3.3 App lifecycle events  
       3.4 Multiple and child windows  
   4. **[Inter-Process Communication](./[4]-Inter-Process-Communication.md)**  
       4.1 ipcMain and ipcRenderer  
       4.2 Two-way calls with invoke and handle  
       4.3 Sending messages from main to renderer  
       4.4 Validating IPC messages  
   5. **[Preload Scripts And Context Isolation](./[5]-Preload-Scripts-And-Context-Isolation.md)**  
       5.1 Why context isolation exists  
       5.2 contextBridge  
       5.3 Designing a safe API surface  
       5.4 Typing the preload API with TypeScript  

**Native Features**

   6. **[Menus, Tray, And Notifications](./[6]-Menus-Tray-And-Notifications.md)**  
       6.1 Application menus  
       6.2 Context menus  
       6.3 Tray icons  
       6.4 Notifications  
   7. **[Dialogs And The File System](./[7]-Dialogs-And-The-File-System.md)**  
       7.1 Open and save dialogs  
       7.2 Reading and writing files  
       7.3 App data and special paths  
       7.4 Drag and drop  
   8. **[Shortcuts, Clipboard, And Protocols](./[8]-Shortcuts-Clipboard-And-Protocols.md)**  
       8.1 Global and local shortcuts  
       8.2 The clipboard  
       8.3 Custom protocols and deep links  
       8.4 Opening external links safely  
   9. **[Data Storage And Settings](./[9]-Data-Storage-And-Settings.md)**  
       9.1 Storing settings  
       9.2 SQLite with better-sqlite3  
       9.3 Protecting secrets with safeStorage  
       9.4 Migrating stored data  
   10. **[Frontend Frameworks With Electron](./[10]-Frontend-Frameworks-With-Electron.md)**  
       10.1 Plain HTML, CSS, and JavaScript  
       10.2 Vite with React or Vue  
       10.3 Development workflow and hot reload  
       10.4 Remembering window state  

**Hardening And Delivery**

   11. **[Security Best Practices](./[11]-Security-Best-Practices.md)**  
       11.1 The Electron security checklist  
       11.2 Content Security Policy  
       11.3 Navigation and permission control  
       11.4 Electron Fuses and hardening  
   12. **[Debugging And Performance](./[12]-Debugging-And-Performance.md)**  
       12.1 DevTools and debugging the main process  
       12.2 Logging  
       12.3 Startup time and memory use  
       12.4 Crash reporting  
   13. **[Testing Electron Applications](./[13]-Testing-Electron-Applications.md)**  
       13.1 Unit tests for main and renderer code  
       13.2 End-to-end tests with Playwright  
       13.3 Mocking IPC and native APIs  
       13.4 Continuous integration  
   14. **[Packaging And Distribution](./[14]-Packaging-And-Distribution.md)**  
       14.1 Electron Forge makers  
       14.2 electron-builder  
       14.3 Code signing and notarization  
       14.4 ASAR archives and app size  
   15. **[Auto Updates And Releases](./[15]-Auto-Updates-And-Releases.md)**  
       15.1 How auto-updating works  
       15.2 autoUpdater and electron-updater  
       15.3 Publishing releases  
       15.4 Update experience and rollbacks  
   16. **[Next Steps With Electron](./[16]-Next-Steps-With-Electron.md)**  
       16.1 TypeScript with Electron  
       16.2 Native modules  
       16.3 Alternatives like Tauri  
       16.4 Project ideas