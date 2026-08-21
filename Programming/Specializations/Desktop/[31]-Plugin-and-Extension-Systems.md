[Previous](./[30]-Packaging-Native-Dependencies.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[32]-App-Architecture-Patterns.md)

*System Integration*

# Lesson 31 - Plugin & Extension Systems

## 31.1 Why Build a Plugin System

A plugin (or extension) architecture lets third parties — or your own team — add functionality without modifying the core app's source code. This is valuable when an app's use cases are too broad to build in directly (VS Code's extension marketplace, a photo editor's filter plugins) and lets the ecosystem grow independently of the core release schedule.

---

## 31.2 Defining a Plugin Interface

A plugin system starts with a stable **contract**: an interface or set of hooks the core app calls and plugins implement. Keep this contract narrow and versioned, since it becomes a compatibility promise to every plugin author.

```csharp
public interface IExportPlugin
{
    string FormatName { get; }
    Task ExportAsync(Document doc, string outputPath);
}
```

---

## 31.3 Discovering and Loading Plugins

Plugins are typically discovered from a known folder (scanned at startup) and loaded dynamically — via reflection/assembly loading in .NET, dynamic `import()` in Electron, or a `.so`/`.dll` loaded through FFI in native apps. Each plugin usually ships a manifest (name, version, entry point, required permissions) so the host can validate compatibility before loading its code.

---

## 31.4 Sandboxing and Trust

Loaded plugin code runs with significant capability, so a robust plugin system limits what plugins can do: running risky operations (filesystem/network access) through a mediated API rather than raw access, versioning the plugin contract so a plugin built for an old app version fails safely instead of crashing, and — for untrusted third-party plugins — considering a separate process or sandbox rather than loading them in-process at all.

[Previous](./[30]-Packaging-Native-Dependencies.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[32]-App-Architecture-Patterns.md)
