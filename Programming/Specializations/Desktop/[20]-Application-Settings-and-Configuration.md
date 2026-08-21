[Previous](./[19]-Local-Databases.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[21]-Working-with-JSON-and-Serialization.md)

*Data & Storage*

# Lesson 20 - Application Settings & Configuration

## 20.1 What Belongs in Settings

Settings are small pieces of persistent state that shape how the app behaves for a given user: window size/position, theme choice, recently opened files, feature toggles, and API keys/credentials. They're distinct from application *data* (documents, database records) — losing settings is inconvenient, but losing data is a real problem.

---

## 20.2 Where Settings Live

Each OS has a conventional location:

- **Windows**: the Registry, or a config file under `%APPDATA%`.
- **macOS**: `~/Library/Preferences` (`.plist` files) via the `NSUserDefaults`/`UserDefaults` API.
- **Linux**: a config file under `~/.config/<app-name>/`, per the XDG Base Directory spec.

Frameworks usually abstract this behind a single settings API so you don't branch per OS yourself (`Preferences` APIs in .NET MAUI, `electron-store` in Electron).

---

## 20.3 A Simple Settings Pattern

Load settings once at startup into an in-memory object, read/write that object throughout the app, and persist it on change or on exit:

```csharp
public class AppSettings
{
    public string Theme { get; set; } = "system";
    public bool ShowStatusBar { get; set; } = true;
}

var settings = SettingsStore.Load<AppSettings>() ?? new AppSettings();
settings.Theme = "dark";
SettingsStore.Save(settings);
```

---

## 20.4 Sensitive Settings

Never store passwords, tokens, or API keys in a plain settings file. Use the OS's secure credential store instead — Windows Credential Manager, macOS Keychain, Linux Secret Service (via `libsecret`) — accessed through a cross-platform library where possible (e.g. `keytar`), so secrets are encrypted at rest by the OS rather than sitting in a readable config file.

[Previous](./[19]-Local-Databases.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[21]-Working-with-JSON-and-Serialization.md)
