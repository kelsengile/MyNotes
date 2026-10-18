[Previous](./[8]-Local-Storage.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[10]-Native-Device-APIs.md)

*Navigation And Data*

# Lesson 9 - State Management

State is data that changes over time. Some state belongs to one component (use `useState`), but other state is shared by many screens: the logged-in user, a shopping cart, the theme. This lesson covers tools for shared state.

First, separate two kinds of state:

- **Client state** — owned by the app (theme, cart, form drafts, UI toggles).
- **Server state** — data that lives on a server and is fetched (posts, products, profiles).

They are best handled by different tools.

## 9.1 Context

React's built-in **Context** shares a value with every component below a provider, without passing props through every level ("prop drilling").

```jsx
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext(null);

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  const toggle = () => setTheme((t) => (t === 'light' ? 'dark' : 'light'));

  return (
    <ThemeContext.Provider value={{ theme, toggle }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error('useTheme must be used inside ThemeProvider');
  return ctx;
}

// Usage anywhere below the provider
function ThemeButton() {
  const { theme, toggle } = useTheme();
  return <Button title={`Theme: ${theme}`} onPress={toggle} />;
}
```

Strengths: no extra library, ideal for values that rarely change (theme, language, current user).

Weakness: every component that uses a context re-renders when its value changes. For frequently changing data, this can hurt performance.

---

## 9.2 Redux Toolkit and Zustand

**Redux Toolkit** is the modern, recommended way to use Redux. It gives you a single global store, predictable updates, and strong developer tools.

```bash
npm install @reduxjs/toolkit react-redux
```

```jsx
import { configureStore, createSlice } from '@reduxjs/toolkit';
import { Provider, useSelector, useDispatch } from 'react-redux';

const cartSlice = createSlice({
  name: 'cart',
  initialState: { items: [] },
  reducers: {
    addItem: (state, action) => {
      state.items.push(action.payload); // safe: Immer handles immutability
    },
    removeItem: (state, action) => {
      state.items = state.items.filter((i) => i.id !== action.payload);
    },
  },
});

export const { addItem, removeItem } = cartSlice.actions;

export const store = configureStore({
  reducer: { cart: cartSlice.reducer },
});

// Wrap your app
export default function App() {
  return (
    <Provider store={store}>
      <CartScreen />
    </Provider>
  );
}

function CartScreen() {
  const items = useSelector((state) => state.cart.items);
  const dispatch = useDispatch();

  return (
    <View>
      <Text>Items: {items.length}</Text>
      <Button title="Add" onPress={() => dispatch(addItem({ id: Date.now(), name: 'Shoes' }))} />
    </View>
  );
}
```

**Zustand** is a small, simple alternative with very little setup and no provider.

```bash
npm install zustand
```

```jsx
import { create } from 'zustand';

const useCartStore = create((set) => ({
  items: [],
  addItem: (item) => set((state) => ({ items: [...state.items, item] })),
  clear: () => set({ items: [] }),
}));

function CartScreen() {
  const items = useCartStore((state) => state.items);
  const addItem = useCartStore((state) => state.addItem);

  return (
    <View>
      <Text>Items: {items.length}</Text>
      <Button title="Add" onPress={() => addItem({ id: Date.now() })} />
    </View>
  );
}
```

Selecting only the pieces you need (`state.items`) means components re-render only when that piece changes.

Both can **persist** to storage. Zustand has a `persist` middleware, and Redux has `redux-persist`. Use them with AsyncStorage or MMKV from [Lesson 8](./[8]-Local-Storage.md).

---

## 9.3 Server state with TanStack Query

Server data has special problems: it can become stale, needs caching, can fail, and may be shared across screens. Storing it in Redux or Context by hand means rewriting the same logic repeatedly.

**TanStack Query** treats server data as a cache:

- Fetches and caches by a `queryKey`.
- Refetches when a screen refocuses, the network reconnects, or data becomes stale.
- Retries failed requests.
- Updates the cache after mutations, including optimistic updates.

```jsx
const { data: user } = useQuery({
  queryKey: ['user', userId],
  queryFn: () => api.get(`/users/${userId}`).then((r) => r.data),
  staleTime: 60 * 1000, // treat data as fresh for 1 minute
});
```

In React Native, connect it to app focus and network state so it refetches at the right times:

```jsx
import { AppState } from 'react-native';
import { focusManager, onlineManager } from '@tanstack/react-query';
import NetInfo from '@react-native-community/netinfo';

onlineManager.setEventListener((setOnline) => {
  return NetInfo.addEventListener((state) => setOnline(!!state.isConnected));
});

AppState.addEventListener('change', (status) => {
  focusManager.setFocused(status === 'active');
});
```

A healthy structure is TanStack Query for server data, and a small Zustand store or Context for client-only state.

---

## 9.4 Choosing an approach

| Situation | Suggested tool |
|---|---|
| State used by one component | `useState` / `useReducer` |
| A few shared values that rarely change (theme, locale) | Context |
| Shared client state with frequent updates | Zustand |
| Large app, many developers, strict patterns, powerful devtools | Redux Toolkit |
| Data fetched from an API | TanStack Query |
| Values that must survive app restarts | Any of the above plus a persistence layer |

Guidelines:

- **Start simple.** Use local state first and lift it up only when needed.
- **Do not copy server data into global state.** Let a query library own it.
- **Keep state minimal.** Derive values (like totals) instead of storing them.
- **Avoid mixing many global solutions** without a clear reason.
- **Keep state close to where it is used.** Global state should be the exception, not the default.

---

[Previous](./[8]-Local-Storage.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[10]-Native-Device-APIs.md)