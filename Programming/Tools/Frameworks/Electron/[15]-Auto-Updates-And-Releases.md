[Previous](./[14]-Packaging-And-Distribution.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[16]-Next-Steps-With-Electron.md)

*Hardening And Delivery*

# Lesson 15 - Auto Updates And Releases

## 15.1 How auto-updating works

Desktop users rarely reinstall manually, so your app should **update itself**. The general flow is the same for every tool:

1. Your app asks an **update server** (or a release host) whether a newer version exists, usually by reading a small metadata file.
2. If one exists, the app **downloads** the new build in the background.
3. When the download finishes, the user is told and the app **restarts into the new version**.

```
App starts ──► check for update ──► newer version? ──no──► keep running
                                         │yes
                                         ▼
                              download in background
                                         │
                                         ▼
                           notify user ──► quit and install ──► new version
```

Requirements and constraints:

- **Code signing is required on macOS** and strongly recommended on Windows (Lesson 14). Updates are rejected if signatures do not match.
- The built-in `autoUpdater` supports **macOS and Windows**. Linux has no built-in updater; most Linux users update through their package manager, or you can use AppImage with `electron-updater`.
- Use **semantic versioning** (`1.2.3`) in `package.json`. The updater compares versions to decide what is newer.
- Updates should only be fetched over **HTTPS**.
- Only check for updates in packaged apps (`app.isPackaged`), not during development.

---

## 15.2 autoUpdater and electron-updater

There are three common routes.

**Route 1: `update-electron-app` with GitHub (simplest).** For public GitHub repositories using Forge, a small helper plus the free update.electronjs.org service does everything:

```bash
npm install update-electron-app
```

```js
// main.js
const { updateElectronApp } = require('update-electron-app')

if (app.isPackaged) {
  updateElectronApp() // checks GitHub Releases periodically
}
```

**Route 2: Electron's built-in `autoUpdater`** with your own server. You provide a feed URL and handle the events:

```js
const { app, autoUpdater, dialog } = require('electron')

if (app.isPackaged) {
  autoUpdater.setFeedURL({ url: `https://updates.example.com/${process.platform}/${app.getVersion()}` })

  autoUpdater.on('update-downloaded', async (_event, releaseNotes, releaseName) => {
    const { response } = await dialog.showMessageBox({
      type: 'info',
      buttons: ['Restart now', 'Later'],
      title: 'Update ready',
      message: `Version ${releaseName} has been downloaded.`
    })
    if (response === 0) autoUpdater.quitAndInstall()
  })

  autoUpdater.on('error', (err) => console.error('Update error', err))

  autoUpdater.checkForUpdates()
  setInterval(() => autoUpdater.checkForUpdates(), 60 * 60 * 1000) // hourly
}
```

**Route 3: `electron-updater`** (part of electron-builder). It supports GitHub, S3, generic servers, and Linux AppImage, with richer features such as download progress and staged rollouts:

```bash
npm install electron-updater
```

```js
const { autoUpdater } = require('electron-updater')

autoUpdater.autoDownload = true

autoUpdater.on('update-available', (info) => console.log('Found', info.version))
autoUpdater.on('download-progress', (p) => console.log(`${Math.round(p.percent)}%`))
autoUpdater.on('update-downloaded', () => autoUpdater.quitAndInstall())
autoUpdater.on('error', (err) => console.error(err))

app.whenReady().then(() => {
  if (app.isPackaged) autoUpdater.checkForUpdatesAndNotify()
})
```

Match the updater to your packaging tool: `electron-updater` with electron-builder, built-in `autoUpdater` or `update-electron-app` with Forge.

---

## 15.3 Publishing releases

A **release** is a versioned set of files (installers plus update metadata) hosted somewhere the updater can reach.

**GitHub Releases** are the most common host. With electron-builder, configure the provider:

```json
{
  "build": {
    "publish": [{ "provider": "github", "owner": "your-name", "repo": "your-repo" }]
  }
}
```

Publish from the command line (needs a `GH_TOKEN` environment variable):

```bash
npx electron-builder --publish always
```

With Forge, add the GitHub publisher and run `npm run publish`:

```js
// forge.config.js
publishers: [
  {
    name: '@electron-forge/publisher-github',
    config: {
      repository: { owner: 'your-name', name: 'your-repo' },
      prerelease: false,
      draft: true // review before making it public
    }
  }
]
```

A typical release process:

1. Update `version` in `package.json` (for example with `npm version minor`).
2. Commit and **tag** it (`v1.3.0`).
3. CI builds and signs on Windows, macOS, and Linux runners.
4. CI publishes the artifacts to a **draft** release.
5. You test the draft, write release notes, and publish.

Other hosts include Amazon S3, your own static file server, or a dedicated update service. electron-builder generates metadata files such as `latest.yml` that the updater reads, so upload them together with the installers.

Use **release channels** (`beta`, `latest`) when you want some users to test early builds first.

---

## 15.4 Update experience and rollbacks

A good update flow respects the user.

**Experience**

- Download **silently in the background** and notify only when ready.
- Offer **Restart now** and **Later**. Do not interrupt unsaved work: ask the page to save first, or only install on the next normal quit with `autoUpdater.autoInstallOnAppQuit = true` (electron-updater).
- Show release notes so users know what changed.
- Add a **Check for updates** menu item for manual checks.
- Handle failures quietly: network errors should log and retry later, not show scary dialogs.

**Releasing safely**

- Test updating from **old versions** to the new one, not only a fresh install. Data migrations (Lesson 9) run here.
- Use **staged rollouts** (electron-updater supports `stagingPercentage` in the metadata) to release to a small percentage first and watch crash reports (Lesson 12).
- Keep a **kill switch** in mind: a way to stop offering a bad release, such as removing or unpublishing it on your host.

**Rollbacks**

Updaters generally move **forward only**; they do not install an older version automatically. If a release is broken:

1. **Stop distribution** of the bad version (delete or unpublish the release so no more users receive it).
2. **Publish a fixed version with a higher number** (for example `1.3.1`), which is the safe and standard way to roll back, even if the code is a revert of `1.3.0`.
3. Optionally allow downgrades deliberately with `autoUpdater.allowDowngrade = true` in electron-updater, but use it with care.

Never reuse or overwrite a version number that users may already have installed, because updaters rely on version numbers being unique.

---

[Previous](./[14]-Packaging-And-Distribution.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[16]-Next-Steps-With-Electron.md)