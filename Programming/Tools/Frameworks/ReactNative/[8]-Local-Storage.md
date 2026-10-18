[Previous](./[7]-Networking-And-APIs.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[9]-State-Management.md)

*Navigation And Data*

# Lesson 8 - Local Storage

Apps often need to keep data on the device: settings, drafts, cached responses, or a full offline database. React Native has no `localStorage`, so you choose a storage library that fits your data.

| Need | Good choice |
|---|---|
| Small settings and simple values | AsyncStorage or MMKV |
| Fast key-value access | MMKV |
| Structured, queryable data | SQLite |
| Tokens, passwords, secrets | Secure storage |

## 8.1 AsyncStorage

**AsyncStorage** is a simple, asynchronous, unencrypted key-value store. Values are strings.

```bash
npx expo install @react-native-async-storage/async-storage
```

```jsx
import AsyncStorage from '@react-native-async-storage/async-storage';

// Save a string
await AsyncStorage.setItem('theme', 'dark');

// Read a string
const theme = await AsyncStorage.getItem('theme'); // 'dark' or null

// Save an object (convert to JSON first)
await AsyncStorage.setItem('user', JSON.stringify({ name: 'Ana', age: 30 }));

// Read an object
const json = await AsyncStorage.getItem('user');
const user = json ? JSON.parse(json) : null;

// Delete
await AsyncStorage.removeItem('theme');

// Delete everything (use carefully)
await AsyncStorage.clear();
```

**Load on startup with a hook**

```jsx
function useStoredTheme() {
  const [theme, setTheme] = useState('light');

  useEffect(() => {
    AsyncStorage.getItem('theme').then((value) => {
      if (value) setTheme(value);
    });
  }, []);

  const updateTheme = async (next) => {
    setTheme(next);
    await AsyncStorage.setItem('theme', next);
  };

  return [theme, updateTheme];
}
```

Good for: preferences, small caches, onboarding flags. Not good for: large datasets, sensitive data, or frequently changing data.

---

## 8.2 SQLite and MMKV

**SQLite** is a full relational database stored in a file on the device. Use it for large or structured data that you need to filter, sort, or join. Expo provides `expo-sqlite`.

```bash
npx expo install expo-sqlite
```

```jsx
import * as SQLite from 'expo-sqlite';

const db = await SQLite.openDatabaseAsync('app.db');

// Create a table
await db.execAsync(`
  CREATE TABLE IF NOT EXISTS todos (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    done INTEGER NOT NULL DEFAULT 0
  );
`);

// Insert (use placeholders, never string concatenation)
await db.runAsync('INSERT INTO todos (title) VALUES (?)', 'Learn React Native');

// Read many rows
const todos = await db.getAllAsync('SELECT * FROM todos WHERE done = ?', 0);

// Update
await db.runAsync('UPDATE todos SET done = 1 WHERE id = ?', 1);

// Delete
await db.runAsync('DELETE FROM todos WHERE id = ?', 1);
```

**MMKV** is a very fast key-value store from WeChat. It reads synchronously, which makes it much quicker than AsyncStorage. It needs a development build (it does not run in Expo Go).

```bash
npm install react-native-mmkv
```

```jsx
import { MMKV } from 'react-native-mmkv';

const storage = new MMKV();

storage.set('username', 'ana');
storage.set('loggedIn', true);
storage.set('score', 42);

const username = storage.getString('username');
const loggedIn = storage.getBoolean('loggedIn');

storage.delete('score');
```

Note: the API above is the version 3 style. Newer major versions changed how a storage instance is created, so check the library's documentation for the version you install.

---

## 8.3 Secure storage

AsyncStorage and MMKV store data in plain form. Anyone with access to the device files (for example on a rooted phone) could read it. For sensitive values, use the platform's secure hardware-backed storage: the **Keychain** on iOS and the **Keystore** on Android.

```bash
npx expo install expo-secure-store
```

```jsx
import * as SecureStore from 'expo-secure-store';

await SecureStore.setItemAsync('authToken', 'abc123');
const token = await SecureStore.getItemAsync('authToken');
await SecureStore.deleteItemAsync('authToken');
```

Use secure storage for: access and refresh tokens, API credentials for the current user, and encryption keys.

Limits to know:

- It is meant for **small values** (not large data).
- Data may be removed when the app is uninstalled, depending on the platform.
- It is not a replacement for server-side security.

---

## 8.4 Offline-first basics

An **offline-first** app works without a connection and syncs when the network returns. Mobile users often have poor or no signal, so this is a big quality improvement.

Core ideas:

1. **Read from local storage first.** Display cached data immediately, then update it from the network.
2. **Write locally first.** Save user changes on the device and queue them for the server.
3. **Sync in the background.** Send queued changes when the connection returns, and handle conflicts.
4. **Show connection state.** Tell the user when they are offline.

**Detect connectivity**

```bash
npx expo install @react-native-community/netinfo
```

```jsx
import NetInfo from '@react-native-community/netinfo';

useEffect(() => {
  const unsubscribe = NetInfo.addEventListener((state) => {
    console.log('Connected:', state.isConnected, 'Internet:', state.isInternetReachable);
  });
  return unsubscribe;
}, []);
```

**A simple caching pattern**

```jsx
async function loadPosts() {
  const cached = await AsyncStorage.getItem('posts');
  if (cached) setPosts(JSON.parse(cached)); // show old data right away

  try {
    const res = await fetch('https://api.example.com/posts');
    const fresh = await res.json();
    setPosts(fresh);
    await AsyncStorage.setItem('posts', JSON.stringify(fresh));
  } catch {
    // offline: keep showing cached data
  }
}
```

Conflict handling strategies include "last write wins", timestamps or version numbers, and merging by field. TanStack Query can also persist its cache to storage (see [Lesson 9](./[9]-State-Management.md)). For complex sync needs, look at local-first databases such as WatermelonDB or RxDB.

---

[Previous](./[7]-Networking-And-APIs.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[9]-State-Management.md)