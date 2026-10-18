[Previous](./[10]-Native-Device-APIs.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[12]-Platform-Specific-Code.md)

*Device Features*

# Lesson 11 - Animations And Gestures

Smooth motion makes an app feel polished. The goal is 60 frames per second (or 120 on high-refresh screens), which means each frame has only about 16 milliseconds (or 8) to draw.

## 11.1 The Animated API

`Animated` is built into React Native. You create an animated value, attach it to a style of an animated component, and drive it with a function like `timing` or `spring`.

```jsx
import { useRef, useEffect } from 'react';
import { Animated, View, Button } from 'react-native';

export default function FadeIn() {
  const opacity = useRef(new Animated.Value(0)).current;

  useEffect(() => {
    Animated.timing(opacity, {
      toValue: 1,
      duration: 500,
      useNativeDriver: true,
    }).start();
  }, []);

  return (
    <Animated.View style={{ opacity, padding: 20, backgroundColor: 'skyblue' }}>
      <Text>Hello</Text>
    </Animated.View>
  );
}
```

Core pieces:

| Piece | Purpose |
|---|---|
| `Animated.Value` | A number that can change over time |
| `Animated.timing` | Animate over a set duration with easing |
| `Animated.spring` | Physics-based spring motion |
| `Animated.sequence` / `parallel` / `stagger` | Combine animations |
| `value.interpolate` | Map a value range to another (for example 0-1 to 0-360deg) |
| `Animated.View`, `Animated.Text`, `Animated.Image`, `Animated.ScrollView` | Animatable components |

**Interpolation example (rotation)**

```jsx
const spin = rotation.interpolate({
  inputRange: [0, 1],
  outputRange: ['0deg', '360deg'],
});

<Animated.View style={{ transform: [{ rotate: spin }] }} />
```

**`useNativeDriver: true`** sends the animation to the native side so it runs without the JavaScript thread. It works for `opacity` and `transform` but **not** for layout properties like `width`, `height`, or `margin`.

---

## 11.2 Reanimated

**React Native Reanimated** is the most powerful animation library. Animations are defined as small functions called **worklets** that run directly on the UI thread, so they stay smooth even when JavaScript is busy.

```bash
npx expo install react-native-reanimated
```

(Newer versions of Reanimated also require the `react-native-worklets` package. Expo and the library's installation guide explain the exact setup for your version.)

```jsx
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  withTiming,
} from 'react-native-reanimated';
import { Button, View } from 'react-native';

export default function Box() {
  const offset = useSharedValue(0);

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ translateX: offset.value }],
  }));

  return (
    <View style={{ padding: 20 }}>
      <Animated.View
        style={[{ width: 100, height: 100, backgroundColor: 'tomato' }, animatedStyle]}
      />
      <Button title="Move right" onPress={() => (offset.value = withSpring(offset.value + 100))} />
      <Button title="Reset" onPress={() => (offset.value = withTiming(0, { duration: 300 }))} />
    </View>
  );
}
```

Main building blocks:

- **`useSharedValue`** — a value readable on both the JS and UI threads. Read and write it using `.value`.
- **`useAnimatedStyle`** — builds a style from shared values.
- **`withTiming`, `withSpring`, `withDelay`, `withRepeat`, `withSequence`** — animation helpers.
- **Layout animations** — such as `FadeIn`, `FadeOut`, and `Layout`, which animate items entering, leaving, or moving.

```jsx
import Animated, { FadeInDown, FadeOut } from 'react-native-reanimated';

<Animated.View entering={FadeInDown.duration(400)} exiting={FadeOut}>
  <Text>I animate in and out</Text>
</Animated.View>
```

---

## 11.3 Gesture Handler

**React Native Gesture Handler** recognizes touches (drag, pinch, rotate, long press) natively. Combined with Reanimated, you can build draggable and swipeable UI that tracks the finger exactly.

```bash
npx expo install react-native-gesture-handler
```

Wrap your app root once with `GestureHandlerRootView`.

**A draggable box**

```jsx
import { GestureHandlerRootView, Gesture, GestureDetector } from 'react-native-gesture-handler';
import Animated, { useSharedValue, useAnimatedStyle, withSpring } from 'react-native-reanimated';

export default function Draggable() {
  const x = useSharedValue(0);
  const y = useSharedValue(0);
  const startX = useSharedValue(0);
  const startY = useSharedValue(0);

  const pan = Gesture.Pan()
    .onStart(() => {
      startX.value = x.value;
      startY.value = y.value;
    })
    .onUpdate((e) => {
      x.value = startX.value + e.translationX;
      y.value = startY.value + e.translationY;
    })
    .onEnd(() => {
      x.value = withSpring(0);
      y.value = withSpring(0);
    });

  const style = useAnimatedStyle(() => ({
    transform: [{ translateX: x.value }, { translateY: y.value }],
  }));

  return (
    <GestureHandlerRootView style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
      <GestureDetector gesture={pan}>
        <Animated.View style={[{ width: 100, height: 100, backgroundColor: 'gold' }, style]} />
      </GestureDetector>
    </GestureHandlerRootView>
  );
}
```

Available gestures: `Tap`, `Pan`, `Pinch`, `Rotation`, `LongPress`, `Fling`, and `Native`. Combine them with `Gesture.Simultaneous(a, b)`, `Gesture.Exclusive(a, b)`, or `Gesture.Race(a, b)`.

Gesture Handler also provides ready-made components like `Swipeable` rows and is used by React Navigation for drawer and back-swipe gestures.

---

## 11.4 Performance considerations

Animation problems usually show up as stutter or dropped frames. Keep these rules in mind:

1. **Keep animations off the JavaScript thread.** Use `useNativeDriver: true` or Reanimated worklets.
2. **Animate `transform` and `opacity`.** These are cheap. Animating `width`, `height`, or `top` forces layout recalculation on every frame.
3. **Do not call `setState` on every frame.** Use shared values or animated values instead.
4. **Avoid heavy work during animations.** Defer big computations with `InteractionManager.runAfterInteractions`.
5. **Keep views simple.** Large images and many nested views slow rendering. Resize images to the size you display.
6. **Test on a release build and a real, mid-range device.** Development mode is slower, and simulators are not representative.
7. **Respect reduced motion.** Some users enable "reduce motion" in system settings. Check it with `AccessibilityInfo.isReduceMotionEnabled()` and tone down animations.

Measure with the performance monitor (open the dev menu and choose "Show Perf Monitor") to see the UI and JS frame rates. See [Lesson 16](./[16]-Performance-Optimization.md) for more profiling techniques.

---

[Previous](./[10]-Native-Device-APIs.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[12]-Platform-Specific-Code.md)