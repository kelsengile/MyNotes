[Previous](./[6]-Handling-User-Input-And-Forms.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[8]-Local-Storage.md)

*Navigation And Data*

# Lesson 7 - Networking And APIs

## 7.1 fetch and Axios

React Native includes the standard `fetch` API, so you can call REST APIs the same way as in a browser.

```jsx
// GET
const response = await fetch('https://jsonplaceholder.typicode.com/posts/1');
if (!response.ok) {
  throw new Error(`Request failed with status ${response.status}`);
}
const post = await response.json();

// POST
const created = await fetch('https://jsonplaceholder.typicode.com/posts', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ title: 'Hello', body: 'World', userId: 1 }),
});
```

Important: `fetch` only rejects on network failure. An HTTP error such as 404 or 500 still resolves, so you must check `response.ok`.

**Axios** is a popular library that adds conveniences: automatic JSON handling, errors thrown for bad status codes, interceptors, timeouts, and request cancellation.

```bash
npm install axios
```

```jsx
import axios from 'axios';

const api = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
});

const { data } = await api.get('/users/1');
await api.post('/users', { name: 'Ana' });
```

**Connection tips**

- Use **HTTPS**. iOS blocks plain HTTP by default (App Transport Security), and Android blocks cleartext traffic in newer versions.
- The Android emulator reaches your computer's `localhost` at **`10.0.2.2`**. The iOS Simulator can use `localhost`.
- A real device needs your computer's local network IP (for example `http://192.168.1.10:3000`) and must be on the same network.

---

## 7.2 Loading and error states

A network request has at least three outcomes: still loading, failed, and succeeded. Your UI should show each one.

```jsx
import { useEffect, useState } from 'react';
import { View, Text, ActivityIndicator, Button, FlatList } from 'react-native';

export default function Posts() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  const load = async () => {
    setLoading(true);
    setError(null);
    try {
      const res = await fetch('https://jsonplaceholder.typicode.com/posts?_limit=10');
      if (!res.ok) throw new Error(`Status ${res.status}`);
      setPosts(await res.json());
    } catch (e) {
      setError(e.message);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    load();
  }, []);

  if (loading) return <ActivityIndicator size="large" />;

  if (error) {
    return (
      <View>
        <Text>Something went wrong: {error}</Text>
        <Button title="Try again" onPress={load} />
      </View>
    );
  }

  return (
    <FlatList
      data={posts}
      keyExtractor={(p) => String(p.id)}
      renderItem={({ item }) => <Text style={{ padding: 12 }}>{item.title}</Text>}
    />
  );
}
```

Good habits:

- Always handle errors and offer a retry option.
- Show an empty state when the result list is empty.
- Cancel or ignore requests when a component unmounts, using `AbortController`.
- Use pull to refresh with `FlatList`'s `refreshing` and `onRefresh` props.

---

## 7.3 Data fetching libraries

Writing loading, error, caching, and retry logic by hand in every screen gets repetitive. **TanStack Query** (React Query) manages **server state** for you.

```bash
npm install @tanstack/react-query
```

```jsx
import { QueryClient, QueryClientProvider, useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

const queryClient = new QueryClient();

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Posts />
    </QueryClientProvider>
  );
}

function Posts() {
  const { data, isLoading, isError, error, refetch, isRefetching } = useQuery({
    queryKey: ['posts'],
    queryFn: async () => {
      const res = await fetch('https://jsonplaceholder.typicode.com/posts?_limit=10');
      if (!res.ok) throw new Error('Failed to load');
      return res.json();
    },
  });

  if (isLoading) return <ActivityIndicator />;
  if (isError) return <Text>{error.message}</Text>;

  return (
    <FlatList
      data={data}
      keyExtractor={(p) => String(p.id)}
      renderItem={({ item }) => <Text>{item.title}</Text>}
      refreshing={isRefetching}
      onRefresh={refetch}
    />
  );
}

// Changing data
function AddPost() {
  const client = useQueryClient();
  const mutation = useMutation({
    mutationFn: (newPost) =>
      fetch('https://jsonplaceholder.typicode.com/posts', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newPost),
      }).then((r) => r.json()),
    onSuccess: () => client.invalidateQueries({ queryKey: ['posts'] }),
  });

  return <Button title="Add" onPress={() => mutation.mutate({ title: 'New' })} />;
}
```

Benefits: automatic caching, background refetching, retries, deduplicated requests, pagination helpers, and simple loading and error flags. **SWR** is a lighter alternative.

---

## 7.4 Authentication tokens

Most APIs use tokens to know who is calling. A common flow:

1. The user logs in with credentials.
2. The server returns an **access token** (short-lived) and often a **refresh token** (long-lived).
3. The app sends the access token with every request in the `Authorization` header.
4. When the access token expires, the app uses the refresh token to get a new one.

**Where to store tokens**

Store tokens in **secure storage** (`expo-secure-store`), which uses the iOS Keychain and Android Keystore. Do not store them in plain AsyncStorage. See [Lesson 8](./[8]-Local-Storage.md).

**Attach the token automatically with an Axios interceptor**

```jsx
import axios from 'axios';
import * as SecureStore from 'expo-secure-store';

const api = axios.create({ baseURL: 'https://api.example.com' });

api.interceptors.request.use(async (config) => {
  const token = await SecureStore.getItemAsync('accessToken');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

api.interceptors.response.use(
  (response) => response,
  async (error) => {
    if (error.response?.status === 401 && !error.config._retry) {
      error.config._retry = true;
      const refreshToken = await SecureStore.getItemAsync('refreshToken');
      const { data } = await axios.post('https://api.example.com/auth/refresh', { refreshToken });
      await SecureStore.setItemAsync('accessToken', data.accessToken);
      error.config.headers.Authorization = `Bearer ${data.accessToken}`;
      return api(error.config);
    }
    return Promise.reject(error);
  }
);

export default api;
```

Security reminders:

- Never put secret API keys in your app code. Anything shipped in the app can be extracted. Keep secrets on your server.
- Clear stored tokens on logout.
- For third-party sign-in (Google, Apple), use OAuth with `expo-auth-session` or the provider's official library.

---

[Previous](./[6]-Handling-User-Input-And-Forms.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[8]-Local-Storage.md)