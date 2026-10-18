[Previous](./[15]-Testing-React-Native-Apps.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[17]-Building-And-Publishing.md)

*Quality And Delivery*

# Lesson 16 - Performance Optimization

A fast app feels responsive: screens open quickly, lists scroll smoothly, and taps react immediately. Always **measure before optimizing**, because guessing often wastes time on code that was not the problem.

Test performance in a **release build on a real device**. Development mode is much slower, and simulators do not reflect real hardware.

## 16.1 List optimization

Long lists are the most common source of performance problems.

**Use the right component**

- Use `FlatList` (or `SectionList`) for long lists, never `ScrollView` with `map`.
- Always provide a stable **`keyExtractor`**.

**Tune `FlatList`**

```jsx
<FlatList
  data={items}
  keyExtractor={(item) => item.id}
  renderItem={renderItem}
  initialNumToRender={10}      // items rendered on first paint
  maxToRenderPerBatch={10}     // items rendered per batch while scrolling
  windowSize={7}               // how many screens of content to keep rendered
  removeClippedSubviews        // detach offscreen views (mainly Android)
  getItemLayout={(data, index) => ({
    length: ITEM_HEIGHT,
    offset: ITEM_HEIGHT * index,
    index,
  })}
/>
```

- **`getItemLayout`** only works if every item has a fixed, known height. It lets the list skip measuring.
- Keep `renderItem` **fast and light**. Avoid heavy calculations inside it.
- Define `renderItem` outside the render body or wrap it in `useCallback`, and put each row in `React.memo`.

```jsx
const Row = React.memo(function Row({ item }) {
  return (
    <View style={styles.row}>
      <Text>{item.title}</Text>
    </View>
  );
});

const renderItem = useCallback(({ item }) => <Row item={item} />, []);
```

**Consider FlashList**

`@shopify/flash-list` is a drop-in replacement for `FlatList` that **recycles views**, which often scrolls much more smoothly.

```bash
npx expo install @shopify/flash-list
```

```jsx
import { FlashList } from '@shopify/flash-list';

<FlashList
  data={items}
  renderItem={({ item }) => <Row item={item} />}
  estimatedItemSize={72}   // required in some versions; check the docs for yours
  keyExtractor={(item) => item.id}
/>
```

**Images in lists**

- Resize images to the displayed size on your server or CDN.
- Use `expo-image`, which caches and decodes efficiently.
- Paginate with `onEndReached` instead of loading everything at once.

---

## 16.2 Avoiding re-renders

React re-renders a component when its state, props, or context changes (and re-renders its children by default). Unnecessary re-renders waste time.

**Tools**

| Tool | Use |
|---|---|
| `React.memo(Component)` | Skip re-rendering if props are unchanged |
| `useCallback(fn, deps)` | Keep the same function reference between renders |
| `useMemo(() => value, deps)` | Cache an expensive calculation |
| Selectors (Redux/Zustand) | Subscribe only to the data a component needs |

```jsx
const Parent = () => {
  const [count, setCount] = useState(0);

  // Without useCallback, a new function is created on every render,
  // which would defeat React.memo on the child.
  const handlePress = useCallback(() => {
    console.log('pressed');
  }, []);

  return (
    <>
      <Button title={`Count ${count}`} onPress={() => setCount((c) => c + 1)} />
      <ExpensiveChild onPress={handlePress} />
    </>
  );
};

const ExpensiveChild = React.memo(({ onPress }) => {
  // expensive rendering
  return <Pressable onPress={onPress}><Text>Child</Text></Pressable>;
});
```

**React Compiler**

The **React Compiler** is a build tool that automatically adds memoization for you, which reduces the need to write `memo`, `useMemo`, and `useCallback` by hand. It can be enabled in recent Expo projects. Check the Expo and React documentation for current setup steps.

**Other techniques**

- **Keep state local.** State that only one component needs should not live in a global store or high in the tree.
- **Split big components** so a state change only re-renders the small part that uses it.
- **Avoid inline objects and functions** passed to memoized children, since they create new references each render.
- **Split context** into several smaller contexts so changes do not affect unrelated consumers.
- **Do not over-memoize.** Memoization itself costs a little, so use it where measurements show a benefit.

---

## 16.3 Hermes engine

**Hermes** is a JavaScript engine built by Meta specifically for React Native. It is the default engine in current React Native and Expo versions.

Why it helps:

- **Faster startup** — JavaScript is compiled ahead of time into **bytecode** during the build, so the app does not need to parse source code at launch.
- **Lower memory use** — a smaller footprint, which matters on budget phones.
- **Smaller app size** — compared with some alternatives.

What you can do to benefit further:

- Make sure Hermes is enabled (it is by default, and you can verify with `global.HermesInternal`, which is defined when Hermes is running).
- Keep your **JavaScript bundle small**: remove unused libraries, import only what you use, and avoid heavy packages where a light alternative exists.
- Load heavy screens or features **lazily** when they are needed.
- Use release builds, since bytecode compilation and optimizations only apply there.

```jsx
const isHermes = () => !!global.HermesInternal;
console.log('Hermes enabled:', isHermes());
```

Keep startup work minimal: avoid long synchronous tasks in your root component, delay non-essential initialization (analytics, large cache loads) until after the first screen shows, and keep the splash screen up only as long as necessary.

---

## 16.4 Profiling

Profiling shows where time and memory are actually spent.

**In-app performance monitor**

Open the dev menu and choose **Show Perf Monitor**. It shows the **UI frame rate** and the **JS frame rate**. If the JS frame rate drops, your JavaScript is the bottleneck. If the UI frame rate drops, native rendering is the problem.

**React DevTools Profiler**

In React Native DevTools, open the **Profiler** tab, click record, use the app, and stop. The flame graph shows which components rendered and how long they took, and why they re-rendered if you enable that option.

**Native profilers**

| Tool | Platform | Finds |
|---|---|---|
| Xcode Instruments (Time Profiler, Allocations, Leaks) | iOS | CPU hot spots, memory leaks |
| Android Studio Profiler | Android | CPU, memory, network, energy |
| Perfetto / System Trace | Android | Frame timing and scheduling |

**Measure startup and bundle size**

```bash
npx expo export --platform android   # inspect the generated bundle size
```

Tools such as `react-native-bundle-visualizer` show which dependencies take up the most space.

**A simple optimization workflow**

1. **Define a goal**, such as "list scrolls at 60 fps" or "startup under 2 seconds".
2. **Measure** on a release build and a mid-range device.
3. **Find the bottleneck** with a profiler.
4. **Change one thing** at a time.
5. **Measure again** to confirm the improvement.
6. Add **monitoring** in production (for example Sentry or Firebase Performance) to catch real-world slowdowns and crashes.

---

[Previous](./[15]-Testing-React-Native-Apps.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[17]-Building-And-Publishing.md)