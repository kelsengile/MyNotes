[Previous](./[39]-Code-Signing-and-Notarization.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[41]-Distributing-via-App-Stores.md)

*Distribution*

# Lesson 40 - Installers & Auto-Updates

## 40.1 Installer Formats

Each platform has native installer formats: Windows uses `.msi` (Windows Installer database) or `.exe` (via WiX, Inno Setup, NSIS); macOS uses `.dmg` (a mountable disk image, typically just drag-to-Applications) or `.pkg` (a scripted installer); Linux uses distro packages (`.deb`, `.rpm`) or self-contained formats (AppImage, Flatpak, Snap). An installer's job is to place files in the right locations, register the app with the OS (Start Menu/Applications/app launcher entries), and optionally set up auto-start or file associations.

---

## 40.2 Why Auto-Update Matters

Desktop apps, unlike web apps, aren't automatically "the latest version" for every user — without an update mechanism, security fixes and improvements only reach whichever users happen to manually reinstall. An auto-updater checks a server for a newer version, downloads it, and applies it, usually on next launch or after a background download completes.

---

## 40.3 Implementing Auto-Updates

Most frameworks have a dedicated update library rather than a hand-rolled solution: `electron-updater` (Electron, often via GitHub Releases or a custom feed), Squirrel (.NET/older Electron), Sparkle (macOS native apps). A typical flow: the app checks a version manifest URL, compares it to its current version, downloads the new package if newer, verifies its signature, and applies it (often via a separate small updater process, since the running app can't usually overwrite its own executable).

```json
{ "version": "2.3.0", "url": "https://example.com/releases/MyApp-2.3.0.exe", "sha256": "..." }
```

---

## 40.4 Update Safety

Always verify a downloaded update's signature/checksum before applying it — an update mechanism is a high-value attack target, since compromising it means compromising every user who updates. Provide a rollback path or at least clear release notes, and avoid forcing silent updates that change behavior without any user-visible changelog.

[Previous](./[39]-Code-Signing-and-Notarization.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[41]-Distributing-via-App-Stores.md)
