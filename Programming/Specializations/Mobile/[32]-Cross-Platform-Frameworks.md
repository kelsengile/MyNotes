[Previous](./[31]-Introduction-to-Android-Development.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[33]-App-Architecture-Patterns.md)

*Platform-Specific Development*

# Lesson 32 - Cross-Platform Frameworks (Flutter, React Native)

## 32.1 Why Cross-Platform?

Building native apps means maintaining two entirely separate codebases (Swift for iOS, Kotlin for Android). Cross-platform frameworks let you write most of your app once and deploy it to both platforms, trading a small amount of performance and platform-specific polish for much faster development and easier maintenance.

---

## 32.2 Flutter

**Flutter** is Google's cross-platform framework, using the **Dart** language. Flutter renders its own UI using a graphics engine rather than relying on native platform components, giving it pixel-perfect consistency across devices.

```dart
class Counter extends StatefulWidget {
  @override
  State<Counter> createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int count = 0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('Count: $count'),
        ElevatedButton(
          onPressed: () => setState(() => count++),
          child: Text('Increment'),
        ),
      ],
    );
  }
}
```

Because Flutter draws every pixel itself, apps look and behave identically on iOS and Android, and hot reload makes iteration fast during development.

---

## 32.3 React Native

**React Native** (by Meta) lets you build mobile apps using **JavaScript/TypeScript** and React's component model. Unlike Flutter, React Native renders using **real native UI components** under the hood — a React Native `<Text>` becomes an actual `UILabel` on iOS or `TextView` on Android.

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <View>
      <Text>Count: {count}</Text>
      <Button title="Increment" onPress={() => setCount(count + 1)} />
    </View>
  );
}
```

This makes React Native apps feel closer to native platform conventions by default, and is a natural fit for teams already fluent in JavaScript/React from web development.

---

## 32.4 Choosing a Cross-Platform Approach

| Factor | Flutter | React Native |
|---|---|---|
| Language | Dart | JavaScript/TypeScript |
| Rendering | Custom engine (Skia) | Native components |
| Performance | Very close to native | Native, with a JS bridge |
| Best fit | Teams wanting UI consistency | Teams already using React/JS |

Both frameworks still allow dropping down to native Swift/Kotlin code for platform-specific features not yet covered by the framework itself, via **native modules/plugins**.

[Previous](./[31]-Introduction-to-Android-Development.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[33]-App-Architecture-Patterns.md)
