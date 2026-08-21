[Previous](./[17]-Data-Binding-and-UI-Updates.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[19]-Local-Databases.md)

*Data & Storage*

# Lesson 18 - File I/O & the Filesystem

## 18.1 Reading and Writing Files

Desktop apps get direct filesystem access — a major difference from web apps. Basic reads and writes look similar across languages:

```csharp
string text = File.ReadAllText(path);
File.WriteAllText(path, updatedText);
```

Always wrap file operations in error handling: the file may not exist, the user may lack permission, or the disk may be full. Use `try/catch` (or the language's equivalent) around I/O rather than assuming it always succeeds.

---

## 18.2 Native File Pickers

Rather than asking users to type a path, use the OS's native open/save dialog, which handles navigation, filtering by file type, and permission prompts consistently with the rest of the system:

```csharp
var dialog = new OpenFileDialog { Filter = "Text files (*.txt)|*.txt" };
if (dialog.ShowDialog() == true) LoadFile(dialog.FileName);
```

---

## 18.3 Platform-Specific Paths

Never hardcode paths like `C:\Users\...` or `/home/user/...` — use the platform's standard-locations API to find the right folder for documents, application data, or temp files (`Environment.GetFolderPath`, `app.getPath()` in Electron, `dirs`/`directories` crates in Rust). This keeps the app correct across Windows, macOS, and Linux, whose conventions for "where should app data live" all differ.

---

## 18.4 Watching for Changes

Some apps need to react when a file changes outside the app itself (e.g. a config file edited manually, or a synced folder updated by another device). File-watching APIs (`FileSystemWatcher` in .NET, `chokidar` in Node, `notify` in Rust) emit events on create/modify/delete/rename, letting the app refresh without polling.

[Previous](./[17]-Data-Binding-and-UI-Updates.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[19]-Local-Databases.md)
