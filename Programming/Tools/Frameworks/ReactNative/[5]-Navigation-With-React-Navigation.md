[Previous](./[4]-Styling-With-StyleSheet.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[6]-Handling-User-Input-And-Forms.md)

*Navigation And Data*

# Lesson 5 - Navigation With React Navigation

React Native has no built-in way to move between screens. The most popular solution is **React Navigation**. Expo Router, the file-based routing system in the default Expo template, is built on top of it, so the concepts here apply to both.

**Install**

```bash
npm install @react-navigation/native @react-navigation/native-stack
npx expo install react-native-screens react-native-safe-area-context
```

## 5.1 Stack navigation

A **stack** works like a pile of cards. Navigating pushes a new screen on top, and going back pops it off.

```jsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { View, Text, Button } from 'react-native';

const Stack = createNativeStackNavigator();

function HomeScreen({ navigation }) {
  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text>Home</Text>
      <Button title="Go to Details" onPress={() => navigation.navigate('Details')} />
    </View>
  );
}

function DetailsScreen({ navigation }) {
  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text>Details</Text>
      <Button title="Go back" onPress={() => navigation.goBack()} />
    </View>
  );
}

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={HomeScreen} options={{ title: 'Welcome' }} />
        <Stack.Screen name="Details" component={DetailsScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

Important methods on the `navigation` object:

| Method | What it does |
|---|---|
| `navigate('Name', params)` | Go to a screen (or return to it if already in the stack) |
| `push('Name', params)` | Always add a new copy of the screen |
| `goBack()` | Go to the previous screen |
| `popToTop()` | Go back to the first screen of the stack |
| `replace('Name')` | Replace the current screen |
| `setOptions({...})` | Change header options from inside a screen |

The native stack uses real native navigation controllers, so you get platform transitions and gestures.

---

## 5.2 Tabs and drawers

**Bottom tabs** let users switch between top-level sections.

```bash
npm install @react-navigation/bottom-tabs
```

```jsx
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

const Tab = createBottomTabNavigator();

function Tabs() {
  return (
    <Tab.Navigator screenOptions={{ headerShown: false }}>
      <Tab.Screen name="Feed" component={FeedScreen} />
      <Tab.Screen name="Profile" component={ProfileScreen} />
    </Tab.Navigator>
  );
}
```

**Drawer** shows a side menu that slides in from the edge.

```bash
npm install @react-navigation/drawer
npx expo install react-native-gesture-handler react-native-reanimated
```

```jsx
import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

function DrawerRoot() {
  return (
    <Drawer.Navigator>
      <Drawer.Screen name="Home" component={HomeScreen} />
      <Drawer.Screen name="Settings" component={SettingsScreen} />
    </Drawer.Navigator>
  );
}
```

**Nesting navigators**

A common structure is a stack that contains a tab navigator as one of its screens. This lets you push full-screen pages (such as a details screen) on top of the tabs.

```jsx
function RootNavigator() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Tabs" component={Tabs} options={{ headerShown: false }} />
      <Stack.Screen name="Details" component={DetailsScreen} />
    </Stack.Navigator>
  );
}
```

---

## 5.3 Passing parameters

Send data to a screen as the second argument of `navigate`, then read it from the `route` prop or the `useRoute` hook.

```jsx
// Sending
<Button
  title="Open product"
  onPress={() => navigation.navigate('Product', { id: 42, name: 'Sneakers' })}
/>

// Receiving
function ProductScreen({ route, navigation }) {
  const { id, name } = route.params;

  useEffect(() => {
    navigation.setOptions({ title: name });
  }, [name]);

  return <Text>Product #{id}: {name}</Text>;
}
```

Good practices:

- Pass **small, simple values** such as IDs and strings. Do not pass large objects or functions.
- Fetch the full data inside the target screen using the ID.
- Provide default values with `initialParams` on `Stack.Screen`.
- Use TypeScript param lists to get type checking for route names and params.

```tsx
type RootStackParamList = {
  Home: undefined;
  Product: { id: number; name: string };
};
```

To go back with data, navigate to the previous screen with new params, or update shared state (see [Lesson 9](./[9]-State-Management.md)).

---

## 5.4 Deep linking

**Deep links** open a specific screen from a URL, such as one tapped in an email, a notification, or a web page.

1. Choose a **URL scheme** in `app.json`:

```json
{
  "expo": {
    "scheme": "myapp"
  }
}
```

2. Describe how URLs map to screens with a `linking` config:

```jsx
const linking = {
  prefixes: ['myapp://', 'https://myapp.example.com'],
  config: {
    screens: {
      Home: 'home',
      Product: 'product/:id',
    },
  },
};

<NavigationContainer linking={linking}>
  {/* navigators */}
</NavigationContainer>
```

With this setup, opening `myapp://product/42` shows the `Product` screen with `route.params.id` set to `"42"`. Note that URL params arrive as strings.

**Universal Links (iOS) and App Links (Android)** use normal `https://` URLs and require you to host a verification file on your website. They are more secure and give a smoother experience than custom schemes.

You can test a deep link from the terminal:

```bash
npx uri-scheme open myapp://product/42 --ios
npx uri-scheme open myapp://product/42 --android
```

---

[Previous](./[4]-Styling-With-StyleSheet.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[6]-Handling-User-Input-And-Forms.md)