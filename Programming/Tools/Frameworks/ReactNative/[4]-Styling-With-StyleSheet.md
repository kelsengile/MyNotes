[Previous](./[3]-Core-Components.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[5]-Navigation-With-React-Navigation.md)

*Basics*

# Lesson 4 - Styling With StyleSheet

## 4.1 StyleSheet API

React Native styles are plain JavaScript objects. `StyleSheet.create` groups them, gives better editor autocomplete, and keeps them organized.

```jsx
import { View, Text, StyleSheet } from 'react-native';

export default function Card() {
  return (
    <View style={styles.card}>
      <Text style={styles.title}>Title</Text>
      <Text style={[styles.body, styles.highlight]}>Body text</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  card: {
    padding: 16,
    borderRadius: 12,
    backgroundColor: '#f3f4f6',
  },
  title: {
    fontSize: 20,
    fontWeight: '600',
  },
  body: {
    fontSize: 16,
    color: '#374151',
  },
  highlight: {
    backgroundColor: '#fef08a',
  },
});
```

Key points:

- Property names are **camelCase**: `backgroundColor`, `borderRadius`, `marginTop`.
- Numbers are **unitless** and measured in density-independent pixels.
- Combine styles with an **array**. Later items override earlier ones.
- Use conditions inside arrays: `[styles.button, isDisabled && styles.disabled]`.
- `StyleSheet.absoluteFill` is a shortcut for a full-size, absolutely positioned view.
- Some style props only apply to certain components. For example, `color` and `fontSize` only work on `Text`.

---

## 4.2 Flexbox layout in React Native

Layout uses **Flexbox**, with a few differences from the web:

- `flexDirection` defaults to **`column`** (not `row`).
- Every `View` is already a flex container.
- `flex: 1` means "take all the available space".

**Main properties**

| Property | Controls | Common values |
|---|---|---|
| `flexDirection` | Main axis direction | `column`, `row`, `column-reverse`, `row-reverse` |
| `justifyContent` | Alignment along the main axis | `flex-start`, `center`, `flex-end`, `space-between`, `space-around`, `space-evenly` |
| `alignItems` | Alignment along the cross axis | `stretch`, `flex-start`, `center`, `flex-end` |
| `alignSelf` | Override alignment for one child | same as `alignItems` |
| `flex` | How much space an item takes | `1`, `2`, ... |
| `flexWrap` | Wrap items onto new lines | `nowrap`, `wrap` |
| `gap`, `rowGap`, `columnGap` | Space between children | numbers |

```jsx
export default function Row() {
  return (
    <View style={styles.container}>
      <View style={[styles.box, { backgroundColor: 'tomato' }]} />
      <View style={[styles.box, { backgroundColor: 'gold' }]} />
      <View style={[styles.box, { backgroundColor: 'skyblue' }]} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    padding: 20,
    gap: 12,
  },
  box: { width: 80, height: 80 },
});
```

**Positioning**: the default is `position: 'relative'`. Use `position: 'absolute'` with `top`, `right`, `bottom`, `left` to place an item over its parent. Use `zIndex` to control stacking.

**Sizing**: you can use fixed numbers, percentage strings (`width: '50%'`), and `aspectRatio` (for example, `aspectRatio: 16 / 9`).

---

## 4.3 Dimensions and safe areas

**Screen size**

Use the `useWindowDimensions` hook. It updates automatically when the device rotates or the window resizes.

```jsx
import { useWindowDimensions, View, Text } from 'react-native';

export default function SizeInfo() {
  const { width, height } = useWindowDimensions();
  return (
    <View>
      <Text>{width} x {height}</Text>
    </View>
  );
}
```

The older `Dimensions.get('window')` returns a one-time value, so it does not react to changes on its own.

**Safe areas**

Modern phones have notches, rounded corners, dynamic islands, and home indicators. Content must avoid these areas. The recommended library is `react-native-safe-area-context` (included with Expo).

```jsx
import { SafeAreaProvider, SafeAreaView, useSafeAreaInsets } from 'react-native-safe-area-context';

// Wrap your app once:
export default function App() {
  return (
    <SafeAreaProvider>
      <Screen />
    </SafeAreaProvider>
  );
}

// Option 1: a component
function Screen() {
  return (
    <SafeAreaView style={{ flex: 1 }}>
      <Text>Safe content</Text>
    </SafeAreaView>
  );
}

// Option 2: a hook for precise control
function Header() {
  const insets = useSafeAreaInsets();
  return <View style={{ paddingTop: insets.top }}><Text>Header</Text></View>;
}
```

Use the library version rather than the built-in `SafeAreaView` from `react-native`, which only works on iOS and is deprecated. Navigation libraries such as React Navigation handle safe areas for their headers and tab bars automatically.

---

## 4.4 Responsive layouts

Phones, tablets, and foldables all have different screen sizes. These tools help your layout adapt:

1. **Flexbox and percentages** — prefer `flex` and `'%'` widths over fixed pixel sizes.
2. **`useWindowDimensions`** — adapt the layout based on width.
3. **`aspectRatio`** — keep images and cards proportional.
4. **`flexWrap`** — let items flow onto new lines.
5. **`Platform.select`** — adjust values per platform (see [Lesson 12](./[12]-Platform-Specific-Code.md)).

```jsx
import { useWindowDimensions, FlatList, Text, View } from 'react-native';

export default function ResponsiveGrid({ data }) {
  const { width } = useWindowDimensions();
  const isTablet = width >= 768;
  const numColumns = isTablet ? 3 : 2;

  return (
    <FlatList
      key={numColumns} // changing numColumns requires a new key
      data={data}
      numColumns={numColumns}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => (
        <View style={{ flex: 1, margin: 8, padding: 16, backgroundColor: '#eee' }}>
          <Text>{item.title}</Text>
        </View>
      )}
    />
  );
}
```

Good habits:

- Avoid hard-coding widths and heights unless the size is truly fixed.
- Test on a small phone, a large phone, and a tablet.
- Test both portrait and landscape orientation.
- Keep touch targets at least about 44x44 points.
- Respect user font scaling. Do not disable it unless you have a good reason.

---

[Previous](./[3]-Core-Components.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[5]-Navigation-With-React-Navigation.md)