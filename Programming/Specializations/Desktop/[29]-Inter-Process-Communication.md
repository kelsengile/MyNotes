[Previous](./[28]-Working-with-the-OS.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[30]-Packaging-Native-Dependencies.md)

*System Integration*

# Lesson 29 - Inter-Process Communication

## 29.1 Why IPC Matters

Some desktop apps are architecturally split into multiple processes — most notably Electron and Tauri, which run a privileged "main"/backend process (system access) separate from one or more "renderer" processes (the UI). These processes can't share memory directly, so they need **Inter-Process Communication (IPC)** to exchange messages.

---

## 29.2 IPC in Electron

Electron's `ipcMain`/`ipcRenderer` modules pass messages between the main and renderer processes, with a `preload` script exposing a safe, limited API to the renderer rather than granting it full Node.js access (a security boundary, covered further in Lesson 45):

```javascript
// main process
ipcMain.handle('save-file', async (event, contents) => {
  await fs.promises.writeFile(path, contents);
});

// renderer, via a preload-exposed API
await window.api.saveFile(documentText);
```

---

## 29.3 IPC Beyond a Single App

Two entirely separate applications can also communicate: named pipes and Unix domain sockets for local process-to-process messaging, D-Bus on Linux for system-wide service communication, and simple approaches like a well-known local port or a shared lock/signal file for lighter needs. Choose based on whether communication is one-directional or request/response, and whether it needs to survive a process restart.

---

## 29.4 Serialization Across the Boundary

Since processes can't share memory, IPC messages must be serialized (Lesson 21) — usually as JSON for Electron/Tauri, or a binary protocol for performance-sensitive native IPC. Keep messages small and specific (a command plus its data) rather than passing large object graphs across the boundary, which adds serialization overhead and couples the two processes tightly.

[Previous](./[28]-Working-with-the-OS.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[30]-Packaging-Native-Dependencies.md)
