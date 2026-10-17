[Previous](./[13]-Testing-Electron-Applications.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[15]-Auto-Updates-And-Releases.md)

*Hardening And Delivery*

# Lesson 14 - Packaging And Distribution

## 14.1 Electron Forge makers

Running `electron .` only works on your machine. To give the app to users you must **package** it (bundle your code with the Electron runtime into an executable) and **make distributables** (installers and archives).

**Electron Forge** is the official all-in-one tool. If you did not scaffold with Forge, add it to an existing project:

```bash
npm install --save-dev @electron-forge/cli
npx electron-forge import
```

Forge's commands:

| Command | What it does |
|---|---|
| `npm start` | Runs the app in development |
| `npm run package` | Creates a packaged app folder for your current OS (no installer) |
| `npm run make` | Packages and then creates installers and archives |
| `npm run publish` | Makes and uploads releases to a provider such as GitHub |

**Makers** produce each output format and are configured in `forge.config.js`:

```js
// forge.config.js
module.exports = {
  packagerConfig: {
    name: 'My App',
    executableName: 'my-app',
    icon: './assets/icon',   // no extension: Forge picks .ico / .icns
    asar: true
  },
  makers: [
    { name: '@electron-forge/maker-squirrel', config: {} },       // Windows installer
    { name: '@electron-forge/maker-zip', platforms: ['darwin'] }, // macOS zip
    { name: '@electron-forge/maker-dmg', config: {} },            // macOS disk image
    { name: '@electron-forge/maker-deb', config: {} },            // Debian/Ubuntu
    { name: '@electron-forge/maker-rpm', config: {} }             // Fedora/RHEL
  ]
}
```

Output goes into the `out/make` folder. Important rule: you generally build installers **on the platform you target** (build the Windows installer on Windows, the macOS app on macOS), which is why CI matrices are common for releases.

---

## 14.2 electron-builder

**electron-builder** is a popular alternative that is configured mostly declaratively and has strong support for auto-update metadata.

```bash
npm install --save-dev electron-builder
```

Add configuration to `package.json` (or a separate `electron-builder.yml`):

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": {
    "dist": "electron-builder"
  },
  "build": {
    "appId": "com.example.myapp",
    "productName": "My App",
    "directories": { "output": "dist" },
    "files": ["main.js", "preload.js", "index.html", "renderer.js", "assets/**"],
    "win": { "target": "nsis" },
    "mac": { "target": ["dmg", "zip"], "category": "public.app-category.productivity" },
    "linux": { "target": ["AppImage", "deb"], "category": "Utility" }
  }
}
```

Build:

```bash
npx electron-builder --win
npx electron-builder --mac
npx electron-builder --linux
```

Common targets: `nsis` (Windows installer), `portable` (single exe), `dmg`, `zip`, `AppImage`, `deb`, `rpm`.

Forge vs electron-builder in short:

- **Forge** is the officially maintained path with plugin support for Vite, webpack, and Fuses, and is the default of the Electron docs.
- **electron-builder** has a rich set of install options and pairs with `electron-updater` (Lesson 15).

Pick one per project; mixing them causes confusion.

---

## 14.3 Code signing and notarization

An unsigned app triggers scary warnings: Windows SmartScreen shows "unknown publisher" and macOS Gatekeeper may refuse to open it at all. **Code signing** proves who built the app and that it has not been tampered with.

**macOS** needs two steps:

1. **Sign** with a *Developer ID Application* certificate from the Apple Developer Program (a paid membership).
2. **Notarize**: upload the signed app to Apple, which scans it and issues a ticket that is stapled to the app.

With Forge:

```js
// forge.config.js
module.exports = {
  packagerConfig: {
    osxSign: {},
    osxNotarize: {
      appleId: process.env.APPLE_ID,
      appleIdPassword: process.env.APPLE_APP_SPECIFIC_PASSWORD,
      teamId: process.env.APPLE_TEAM_ID
    }
  }
}
```

**Windows** needs a code signing certificate (from a certificate authority) or a cloud signing service such as Azure Trusted Signing. SmartScreen reputation builds up over time with signed releases.

With Forge's Squirrel maker:

```js
{
  name: '@electron-forge/maker-squirrel',
  config: {
    certificateFile: './cert.pfx',
    certificatePassword: process.env.CERTIFICATE_PASSWORD
  }
}
```

**Linux** does not require signing for most formats.

Practical advice:

- Keep certificates and passwords in **CI secrets or environment variables**, never in the repository.
- Sign on the same platform you build on (macOS signing needs a Mac).
- Verify signatures: `codesign --verify --deep --strict MyApp.app` on macOS, and check file properties on Windows.
- Auto-updates on macOS **require** a signed app (Lesson 15).

---

## 14.4 ASAR archives and app size

By default your source code is packed into a single archive file called **ASAR** (`app.asar`). It speeds up loading, shortens long Windows paths, and makes casual snooping slightly harder. Electron reads from it transparently, so `fs.readFile` works on paths inside it.

Important details:

- ASAR is **not encryption**. Anyone can extract it with `npx asar extract app.asar out/`. Never ship secrets inside.
- **Native modules** (`.node` files) and anything executed as a separate program must live outside the archive. Unpack them:

```js
// forge.config.js
packagerConfig: {
  asar: { unpack: '**/*.node' }
}
```

In electron-builder: `"asarUnpack": ["**/*.node"]`.

- Files you open with external programs or `child_process` also need to be unpacked.

**Reducing app size**

- Electron itself is the biggest part (roughly 100 MB or more). You cannot remove it, but you can control everything else.
- **Bundle** your code with Vite or esbuild so only used code ships, and exclude `devDependencies` (packagers do this automatically for production builds).
- Remove unneeded locales: `packagerConfig: { ignore: [...] }` or the `electronLanguages` option to keep only the languages you support.
- Avoid shipping big assets you do not need, and compress images.
- Audit dependencies with `npm ls` and replace heavy ones with lighter alternatives.
- Installers compress the payload, so download size is smaller than installed size.

---

[Previous](./[13]-Testing-Electron-Applications.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[15]-Auto-Updates-And-Releases.md)