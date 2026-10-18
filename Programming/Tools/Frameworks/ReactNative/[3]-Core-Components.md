[Previous](./[2]-React-Foundations-For-Native.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[4]-Styling-With-StyleSheet.md)

*Basics*

# Lesson 3 - Core Components

## 3.1 View, Text, and Image

**`View`** is the basic container, similar to a `div`. It is used for layout, grouping, and styling.

**`Text`** displays text. All text must be wrapped in it. Text components can be nested to style parts of a sentence.

```jsx
import { View, Text, Image } from 'react-native';

export default function Profile() {
  return (
    <View>
      <Image
        source={{ uri: 'https://example.com/avatar.png' }}
        style={{ width: 100, height: 100, borderRadius: 50 }}
      />
      <Text numberOfLines={2}>
        Hello, <Text style={{ fontWeight: 'bold' }}>world</Text>!
      </Text>
    </View>
  );
}
```

**`Image`** notes:

- **Local images** use `require`: `source={require('./assets/logo.png')}`. Their size is known automatically.
- **Remote images** use `{ uri: '...' }` and **must have a `width` and `height`** in the style.
- Use the `resizeMode` prop (`cover`, `contain`, `stretch`, `center`) to control scaling.
- For caching and better performance, many projects use the `expo-image` library.

Useful `Text` props: `numberOfLines`, `ellipsizeMode`, `selectable`, and `onPress`.

---

## 3.2 ScrollView and FlatList

**`ScrollView`** renders all its children at once. It is fine for a small amount of content such as a settings page.

```jsx
import { ScrollView, Text } from 'react-native';

<ScrollView contentContainerStyle={{ padding: 16 }}>
  <Text>Item 1</Text>
  <Text>Item 2</Text>
  <Text>Item 3</Text>
</ScrollView>
```

**`FlatList`** renders only the items near the visible area, which makes it the right choice for long lists.

```jsx
import { FlatList, Text, View } from 'react-native';

const DATA = [
  { id: '1', title: 'Apples' },
  { id: '2', title: 'Bananas' },
  { id: '3', title: 'Cherries' },
];

export default function FruitList() {
  return (
    <FlatList
      data={DATA}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => (
        <View style={{ padding: 16 }}>
          <Text>{item.title}</Text>
        </View>
      )}
      ItemSeparatorComponent={() => <View style={{ height: 1, backgroundColor: '#ddd' }} />}
      ListEmptyComponent={<Text>No items found</Text>}
    />
  );
}
```

Other handy props: `ListHeaderComponent`, `ListFooterComponent`, `onEndReached` (for pagination), `refreshing` with `onRefresh` (pull to refresh), and `numColumns` (grids).

For grouped data with section headers, use **`SectionList`**.

Do not put a vertical `FlatList` inside a vertical `ScrollView`. It disables list virtualization and produces warnings.

---

## 3.3 TextInput and Pressable

**`TextInput`** is a controlled input: you store its value in state.

```jsx
import { useState } from 'react';
import { TextInput, Text, View } from 'react-native';

export default function NameField() {
  const [name, setName] = useState('');

  return (
    <View>
      <TextInput
        value={name}
        onChangeText={setName}
        placeholder="Enter your name"
        autoCapitalize="words"
        style={{ borderWidth: 1, padding: 10, borderRadius: 8 }}
      />
      <Text>Hello, {name}</Text>
    </View>
  );
}
```

Common props: `secureTextEntry` (passwords), `keyboardType` (`email-address`, `numeric`, `phone-pad`), `multiline`, `maxLength`, `returnKeyType`, and `onSubmitEditing`.

**`Pressable`** is the flexible way to make anything touchable. It tells you when it is pressed so you can change its style.

```jsx
import { Pressable, Text } from 'react-native';

<Pressable
  onPress={() => console.log('Pressed')}
  onLongPress={() => console.log('Long pressed')}
  style={({ pressed }) => [
    { padding: 12, borderRadius: 8, backgroundColor: '#2563eb' },
    pressed && { opacity: 0.7 },
  ]}
>
  <Text style={{ color: 'white', textAlign: 'center' }}>Save</Text>
</Pressable>
```

**`Button`** is a simple built-in button (`<Button title="Save" onPress={...} />`). It looks different on each platform and has limited styling, so most apps build their own with `Pressable`.

Older touch components (`TouchableOpacity`, `TouchableHighlight`) still work, but `Pressable` is preferred for new code.

---

## 3.4 Platform differences

The same component can look or behave slightly differently on iOS and Android because it uses native widgets.

| Area | iOS | Android |
|---|---|---|
| Shadows | `shadowColor`, `shadowOffset`, `shadowOpacity`, `shadowRadius` | `elevation` |
| Default font | San Francisco | Roboto |
| `Button` look | Text-only, blue | Filled, material style |
| Back navigation | Swipe from the left edge | System back button or gesture |
| Date and time pickers | Inline or wheel style | Dialog style |
| Keyboard behavior | Often needs `KeyboardAvoidingView` | Usually adjusts automatically |

Example of a cross-platform shadow:

```jsx
const styles = StyleSheet.create({
  card: {
    backgroundColor: 'white',
    borderRadius: 12,
    padding: 16,
    // iOS
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.15,
    shadowRadius: 6,
    // Android
    elevation: 4,
  },
});
```

You can also use the `boxShadow` style prop in recent React Native versions, which works on both platforms with the New Architecture.

For deeper handling of differences, see [Lesson 12](./[12]-Platform-Specific-Code.md).

---

[Previous](./[2]-React-Foundations-For-Native.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[4]-Styling-With-StyleSheet.md)