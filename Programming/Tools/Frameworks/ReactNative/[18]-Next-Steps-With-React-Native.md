[Previous](./[17]-Building-And-Publishing.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md)

*Quality And Delivery*

# Lesson 18 - Next Steps With React Native

You now know the full path from a first screen to a published app. This final lesson looks at how React Native compares with alternatives, where to deepen your skills, and what to build next.

## 18.1 Comparing with Flutter

**Flutter** (by Google) is the other major cross-platform framework. It uses the **Dart** language and draws every pixel itself with its own rendering engine, instead of using the platform's native widgets.

| | React Native | Flutter |
|---|---|---|
| Language | JavaScript / TypeScript | Dart |
| UI approach | Native platform views | Custom-drawn widgets (own renderer) |
| Look and feel | Matches the platform automatically | Consistent everywhere, platform feel is recreated |
| Learning curve | Easy if you know React and JavaScript | Requires learning Dart and Flutter's widget model |
| Ecosystem | Huge (all of npm) | Large and growing (pub.dev) |
| Hiring pool | Very large (web developers) | Smaller but growing |
| Code sharing with web | Strong, especially with React | Possible, but a different codebase style |
| Backed by | Meta, Expo, and a large community | Google |

**How to choose**

- Choose **React Native** if your team already knows JavaScript or React, you want to share code or skills with a web app, or you want native look and feel by default.
- Choose **Flutter** if you want pixel-identical UI on every platform, heavy custom graphics, or your team is happy to adopt Dart.
- Choose **fully native** (Swift and Kotlin) when you need the deepest platform integration, cutting-edge platform features on day one, or maximum performance for specialized apps.

No option is best for everyone. The right choice depends on your **team's skills, product needs, and timeline**. Both are used to ship successful, high-quality apps.

---

## 18.2 Advanced navigation

Real apps have more complex navigation than a simple stack.

**Authentication flows**

Show different screens depending on whether the user is signed in:

```jsx
function RootNavigator() {
  const { user, isLoading } = useAuth();

  if (isLoading) return <SplashScreen />;

  return (
    <Stack.Navigator screenOptions={{ headerShown: false }}>
      {user ? (
        <Stack.Screen name="App" component={MainTabs} />
      ) : (
        <>
          <Stack.Screen name="Login" component={LoginScreen} />
          <Stack.Screen name="Register" component={RegisterScreen} />
        </>
      )}
    </Stack.Navigator>
  );
}
```

When `user` changes, React Navigation switches the screens automatically, and the signed-out screens are removed so the user cannot go back to them.

**Other advanced topics**

- **Modals** — present screens over the current one with `presentation: 'modal'` in stack options.
- **Nested navigators** — tabs inside stacks inside drawers. Learn how params and navigation actions travel across levels.
- **Custom headers and tab bars** — fully control the look of navigation UI.
- **Typed navigation** — TypeScript param lists for safe route names and params.
- **Navigation state persistence** — restore the user's place after the app restarts.
- **Shared element transitions** — animate an element between screens.

**Expo Router**

Expo Router is a **file-based router** built on React Navigation. Files in the `app/` folder become routes automatically, similar to Next.js on the web.

```
app/
├── _layout.tsx          # Root layout (stack or tabs)
├── index.tsx            # "/"
├── (tabs)/
│   ├── _layout.tsx      # Tab bar
│   ├── home.tsx         # "/home"
│   └── profile.tsx      # "/profile"
└── product/
    └── [id].tsx         # "/product/42"
```

```tsx
import { Link, useLocalSearchParams } from 'expo-router';

export default function Product() {
  const { id } = useLocalSearchParams();
  return <Text>Product {id}</Text>;
}

// Navigate with:
<Link href="/product/42">Open product</Link>
```

Expo Router gives you **deep linking for free**, typed routes, and web support, which makes it the default choice for new Expo projects.

---

## 18.3 Native integrations

As your app grows you will meet needs beyond the basics.

**Common native features to explore**

- **Authentication** — Sign in with Apple, Google Sign-In, biometrics (`expo-local-authentication`).
- **Payments** — in-app purchases (for example RevenueCat) and card payments (Stripe).
- **Maps and location** — `react-native-maps`, geofencing, background location.
- **Bluetooth and sensors** — BLE devices, accelerometer, gyroscope (`expo-sensors`).
- **Files and sharing** — `expo-file-system`, `expo-sharing`, document pickers.
- **Home screen widgets, App Clips, and share extensions** — require native code and platform-specific setup.
- **Analytics and crash reporting** — Sentry, Firebase Crashlytics, PostHog.
- **Backend services** — Firebase, Supabase, AWS Amplify, or your own API.

**Adding React Native to an existing native app ("brownfield")**

You do not have to start from scratch. React Native can power **one screen or feature** inside an existing iOS or Android app, which is useful for gradual adoption.

**Going deeper into native**

- Write your own modules with the **Expo Modules API** or **TurboModules** (see [Lesson 13](./[13]-Native-Modules-And-The-New-Architecture.md)).
- Learn the basics of **Swift/SwiftUI** and **Kotlin/Jetpack Compose**. Even basic native knowledge helps you debug and extend your app.
- Use **config plugins** to automate native configuration changes without editing native projects by hand.

**Before adding a library, check:**

- Is it **maintained** and compatible with your React Native and Expo version?
- Does it support the **New Architecture**?
- Does it work in **Expo Go**, or does it require a development build?
- How large is it, and does it add permissions you do not need?

---

## 18.4 Project ideas

The best way to learn is to build. Start small, then add features.

**Beginner**

- **To-do list** — add, complete, and delete tasks, saved with AsyncStorage.
- **Unit converter or tip calculator** — practice state, inputs, and styling.
- **Weather app** — fetch from a public API, with loading and error states.
- **Flashcards or quiz app** — lists, navigation, and simple state.

**Intermediate**

- **Expense tracker** — SQLite storage, charts, and category filters.
- **Recipe or movie browser** — API search, `FlatList`, detail screens, favorites.
- **Habit tracker** — local notifications, streaks, and persistent data.
- **Photo journal** — camera, image picker, location tagging, and local storage.

**Advanced**

- **Chat app** — real-time messaging with WebSockets or Firebase, push notifications.
- **Social feed** — authentication, image uploads, infinite scroll, and optimistic updates.
- **Fitness tracker** — sensors, background tasks, maps, and offline sync.
- **E-commerce app** — product catalog, cart, payments, and order history.

**Tips for finishing projects**

1. **Define a small first version** (an MVP) and finish it before adding extras.
2. Use **TypeScript** to catch mistakes early.
3. **Commit often** with clear messages, and keep the project on GitHub.
4. Add a few **tests** and set up **CI**.
5. **Publish** a real build, even to TestFlight or an internal Play track. Releasing teaches you what tutorials cannot.

**Keep learning**

- Official docs: reactnative.dev and docs.expo.dev
- React Navigation docs: reactnavigation.org
- React Native Directory: reactnative.directory for finding compatible libraries
- Read the source and issues of libraries you use, and follow the React Native release notes
- Join the community on GitHub Discussions, Discord, and Reddit

Congratulations on completing the React Native Topic. Keep building, and keep experimenting!

---

[Previous](./[17]-Building-And-Publishing.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md)