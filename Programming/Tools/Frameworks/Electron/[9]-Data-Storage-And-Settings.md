[Previous](./[8]-Shortcuts-Clipboard-And-Protocols.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[10]-Frontend-Frameworks-With-Electron.md)

*Native Features*

# Lesson 9 - Data Storage And Settings

## 9.1 Storing settings

Small amounts of data, like theme or window preferences, fit well in a JSON file inside the `userData` folder. Browser storage (`localStorage`) also works but is tied to the renderer and can be cleared, so keep important data in the main process.

A tiny hand-written settings store:

```js
// settings.js (main process)
const { app } = require('electron')
const path = require('node:path')
const fs = require('node:fs')

const file = path.join(app.getPath('userData'), 'settings.json')
const defaults = { theme: 'light', fontSize: 14 }

function load() {
  try {
    return { ...defaults, ...JSON.parse(fs.readFileSync(file, 'utf8')) }
  } catch {
    return { ...defaults }
  }
}

let settings = load()

function get(key) {
  return settings[key]
}

function set(key, value) {
  settings[key] = value
  // write to a temp file then rename, so a crash cannot corrupt the file
  const tmp = file + '.tmp'
  fs.writeFileSync(tmp, JSON.stringify(settings, null, 2))
  fs.renameSync(tmp, file)
}

module.exports = { get, set }
```

Expose it with IPC:

```js
ipcMain.handle('settings:get', (_e, key) => settings.get(key))
ipcMain.handle('settings:set', (_e, key, value) => {
  if (!['theme', 'fontSize'].includes(key)) throw new Error('Unknown setting')
  settings.set(key, value)
})
```

The **`electron-store`** package wraps this pattern with schema validation, defaults, and migrations. Recent versions are ES modules, so import it with `import Store from 'electron-store'` (or dynamic `import()` from CommonJS):

```js
import Store from 'electron-store'
const store = new Store({ defaults: { theme: 'light' } })
store.set('theme', 'dark')
console.log(store.get('theme'))
```

Do not store secrets in plain settings files (see 9.3).

---

## 9.2 SQLite with better-sqlite3

For structured or larger data (notes, tasks, history) a database is a better fit. **SQLite** is a file-based SQL database that needs no server, and `better-sqlite3` is a fast, synchronous Node binding for it.

```bash
npm install better-sqlite3
```

`better-sqlite3` is a **native module**: it contains compiled C++ code that must match Electron's Node version. Electron Forge and electron-builder rebuild native modules for you during packaging; for manual use you can run `npx @electron/rebuild`.

```js
// db.js (main process only)
const { app } = require('electron')
const path = require('node:path')
const Database = require('better-sqlite3')

const db = new Database(path.join(app.getPath('userData'), 'app.db'))

db.pragma('journal_mode = WAL') // better concurrency and crash safety

db.exec(`
  CREATE TABLE IF NOT EXISTS notes (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    body TEXT NOT NULL DEFAULT '',
    updated_at TEXT NOT NULL DEFAULT CURRENT_TIMESTAMP
  )
`)

const insertNote = db.prepare('INSERT INTO notes (title, body) VALUES (?, ?)')
const allNotes = db.prepare('SELECT id, title, updated_at FROM notes ORDER BY updated_at DESC')

module.exports = {
  addNote: (title, body) => insertNote.run(title, body).lastInsertRowid,
  listNotes: () => allNotes.all()
}
```

```js
ipcMain.handle('notes:add', (_e, title, body) => {
  if (typeof title !== 'string' || typeof body !== 'string') throw new Error('Invalid')
  return db.addNote(title, body)
})
ipcMain.handle('notes:list', () => db.listNotes())
```

Important habits:

- **Always use prepared statements with `?` placeholders.** Never build SQL by joining strings with user input.
- Keep the database in the **main process** only; the renderer asks through IPC.
- Group related writes in a transaction: `db.transaction(() => { ... })()`.
- Close the database on exit: `app.on('will-quit', () => db.close())`.
- With ASAR packaging, native `.node` files must be unpacked (see Lesson 14).

---

## 9.3 Protecting secrets with safeStorage

API tokens, passwords, and refresh tokens must not be saved as plain text. Electron's `safeStorage` encrypts strings using the operating system's credential facilities (Keychain on macOS, DPAPI on Windows, and a secret store such as libsecret/kwallet on Linux).

```js
const { app, safeStorage } = require('electron')
const fs = require('node:fs/promises')
const path = require('node:path')

const tokenFile = path.join(app.getPath('userData'), 'token.bin')

async function saveToken(token) {
  if (!safeStorage.isEncryptionAvailable()) {
    throw new Error('Secure storage is not available on this system')
  }
  const encrypted = safeStorage.encryptString(token) // returns a Buffer
  await fs.writeFile(tokenFile, encrypted)
}

async function loadToken() {
  try {
    const encrypted = await fs.readFile(tokenFile)
    return safeStorage.decryptString(encrypted)
  } catch {
    return null
  }
}
```

Guidelines:

- Call `safeStorage` only after the app is ready.
- Always check `isEncryptionAvailable()`; on some Linux setups there is no secret store, and falling back to plain text would defeat the point.
- Keep decrypted secrets in the **main process**. Do not send them to the renderer unless it truly needs them.
- Encryption protects data at rest from other users and casual file access. It cannot protect against malware running as the same user.

---

## 9.4 Migrating stored data

Your data format will change as the app grows: a new column, a renamed setting, a different file layout. A **migration** upgrades old data to the new format. The key is recording a **version number**.

**Database migrations** with SQLite's built-in `user_version` pragma:

```js
const migrations = [
  // version 1
  `CREATE TABLE notes (id INTEGER PRIMARY KEY, title TEXT, body TEXT)`,
  // version 2
  `ALTER TABLE notes ADD COLUMN updated_at TEXT`,
  // version 3
  `CREATE TABLE tags (note_id INTEGER, tag TEXT)`
]

function migrate(db) {
  const current = db.pragma('user_version', { simple: true })

  const run = db.transaction(() => {
    for (let v = current; v < migrations.length; v++) {
      db.exec(migrations[v])
      db.pragma(`user_version = ${v + 1}`)
    }
  })

  run()
}
```

**Settings migrations** follow the same idea with a `version` field in the JSON:

```js
function migrateSettings(data) {
  if (!data.version) {
    data.fontSize = data.size ?? 14 // renamed key
    delete data.size
    data.version = 1
  }
  if (data.version < 2) {
    data.theme = data.darkMode ? 'dark' : 'light'
    delete data.darkMode
    data.version = 2
  }
  return data
}
```

Best practices:

- Run migrations at startup, **before** any window uses the data.
- Make each step small and run it inside a transaction when possible.
- **Back up** the data file before a risky migration.
- Never edit an old migration after release. Add a new one instead.
- Test upgrades from every previously released version, not only from the latest.

---

[Previous](./[8]-Shortcuts-Clipboard-And-Protocols.md) | [Table of Contents](./[0]-Introduction-to-Electron.md) | [Next](./[10]-Frontend-Frameworks-With-Electron.md)