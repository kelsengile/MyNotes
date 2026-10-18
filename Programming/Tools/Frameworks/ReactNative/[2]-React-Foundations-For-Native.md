[Previous](./[1]-What-Is-React-Native.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[3]-Core-Components.md)

*Basics*

# Lesson 2 - React Foundations For Native

## 2.1 Components and props refresher

A **component** is a function that returns UI. A **prop** is an input passed to a component, like a function argument. Props are read-only.

```jsx
import { View, Text } from 'react-native';

function Greeting({ name, age }) {
  return (
    <View>
      <Text>Hello, {name}!</Text>
      <Text>You are {age} years old.</Text>
    </View>
  );
}

export default function App() {
  return <Greeting name="Maria" age={28} />;
}
```

Useful patterns:

- **Composition** — build large screens from small, reusable components.
- **`children`** — content placed between a component's tags is received as `props.children`.
- **Default values** — use destructuring defaults: `function Badge({ color = 'blue' }) {}`.
- **JSX rules** — return a single root element (or a fragment `<>...</>`), and use `{}` to embed JavaScript.

---

## 2.2 State and hooks

**State** is data that changes over time and causes the component to re-render. Hooks are functions that let components use state and other React features.

```jsx
import { useState, useEffect, useRef } from 'react';
import { View, Text, Button } from 'react-native';

export default function Counter() {
  const [count, setCount] = useState(0);
  const renderCount = useRef(0);

  useEffect(() => {
    console.log('Count changed to', count);
  }, [count]);

  renderCount.current += 1;

  return (
    <View>
      <Text>Count: {count}</Text>
      <Button title="Add one" onPress={() => setCount(count + 1)} />
    </View>
  );
}
```

Common hooks:

| Hook | Purpose |
|---|---|
| `useState` | Store values that trigger re-renders |
| `useEffect` | Run side effects (fetching, subscriptions) after render |
| `useRef` | Hold a mutable value that does **not** trigger re-render |
| `useMemo` / `useCallback` | Cache values or functions between renders |
| `useContext` | Read shared data from a Context |

Rules of hooks: call them only at the top level of a component (not inside loops or conditions) and only inside components or custom hooks.

---

## 2.3 Differences from React for the web

If you know React for the web, most concepts transfer directly. The main differences are in the building blocks:

| Web React | React Native |
|---|---|
| `<div>`, `<span>`, `<p>` | `<View>`, `<Text>` |
| `<img>` | `<Image>` |
| `<input>` | `<TextInput>` |
| `<button>` | `<Pressable>`, `<Button>` |
| `onClick` | `onPress` |
| `onChange` (event) | `onChangeText` (gives the string) |
| CSS files, `className` | `StyleSheet` objects, `style` prop |
| `<a href>` | Navigation library (see [Lesson 5](./[5]-Navigation-With-React-Navigation.md)) |
| DOM, `window`, `document` | Not available |
| `localStorage` | AsyncStorage / MMKV (see [Lesson 8](./[8]-Local-Storage.md)) |

Important rules:

- **All text must be inside a `<Text>` component.** Writing a raw string inside a `<View>` causes an error.
- **Styles are JavaScript objects**, written in camelCase (`backgroundColor`), with unitless numbers.
- **Layout uses Flexbox** by default, and the default direction is column.
- **No CSS cascade** — styles are not inherited, except for some `Text` styles nested inside other `Text` components.

---

## 2.4 Project structure

A new Expo project (using the default template with Expo Router) looks roughly like this. Exact names can vary by template version.

```
MyFirstApp/
├── app/                  # Screens and routes (file-based routing)
│   ├── _layout.tsx       # Root layout
│   └── index.tsx         # The home screen
├── assets/               # Images, fonts, icons
├── components/           # Reusable UI components
├── app.json              # Expo configuration (name, icon, permissions)
├── package.json          # Dependencies and scripts
├── tsconfig.json         # TypeScript settings
└── node_modules/         # Installed packages (never edit)
```

A common structure as the app grows:

```
src/
├── components/   # Shared UI pieces
├── screens/      # One file per screen
├── navigation/   # Navigators and route types
├── hooks/        # Custom hooks
├── services/     # API calls and storage
├── store/        # Global state
└── utils/        # Helper functions
```

Key files:

- **`package.json`** lists your dependencies and scripts such as `npm start`.
- **`app.json`** (or `app.config.js`) holds app name, icons, splash screen, and platform settings.
- **`.gitignore`** should include `node_modules/` and build output.

Use `npx expo install <package>` to add libraries, since it picks versions compatible with your Expo SDK.

---

[Previous](./[1]-What-Is-React-Native.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[3]-Core-Components.md)