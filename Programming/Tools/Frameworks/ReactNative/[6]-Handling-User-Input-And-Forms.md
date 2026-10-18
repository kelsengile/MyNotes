[Previous](./[5]-Navigation-With-React-Navigation.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[7]-Networking-And-APIs.md)

*Navigation And Data*

# Lesson 6 - Handling User Input And Forms

## 6.1 Text input and keyboards

On mobile, the on-screen keyboard is part of your UI. Configure it so typing is easy and it never hides the field.

**Choose the right keyboard**

```jsx
<TextInput
  placeholder="Email"
  keyboardType="email-address"
  autoCapitalize="none"
  autoComplete="email"
  textContentType="emailAddress"
  returnKeyType="next"
/>

<TextInput
  placeholder="Password"
  secureTextEntry
  autoCapitalize="none"
  autoComplete="password"
  returnKeyType="done"
/>
```

Useful props:

| Prop | Purpose |
|---|---|
| `keyboardType` | `default`, `email-address`, `numeric`, `phone-pad`, `decimal-pad` |
| `autoCapitalize` | `none`, `sentences`, `words`, `characters` |
| `returnKeyType` | Label of the return key (`next`, `done`, `search`, `send`) |
| `onSubmitEditing` | Called when the return key is pressed |
| `blurOnSubmit` / `submitBehavior` | Controls whether the keyboard closes on submit |

**Move focus between inputs**

```jsx
const passwordRef = useRef(null);

<TextInput
  placeholder="Email"
  returnKeyType="next"
  onSubmitEditing={() => passwordRef.current?.focus()}
/>
<TextInput ref={passwordRef} placeholder="Password" secureTextEntry />
```

**Keep inputs visible when the keyboard opens**

```jsx
import { KeyboardAvoidingView, Platform, ScrollView, Keyboard, Pressable } from 'react-native';

<KeyboardAvoidingView
  style={{ flex: 1 }}
  behavior={Platform.OS === 'ios' ? 'padding' : undefined}
>
  <ScrollView keyboardShouldPersistTaps="handled">
    {/* form fields */}
  </ScrollView>
</KeyboardAvoidingView>
```

- `keyboardShouldPersistTaps="handled"` lets buttons work while the keyboard is open.
- Call `Keyboard.dismiss()` to close the keyboard, for example when tapping outside an input.
- For more advanced behavior, the `react-native-keyboard-controller` library offers smoother handling.

---

## 6.2 Form libraries

Managing many fields with `useState` becomes repetitive. Form libraries handle values, errors, and submission for you.

**React Hook Form** is a popular choice because it re-renders very little. In React Native you connect inputs using `Controller`.

```bash
npm install react-hook-form
```

```jsx
import { useForm, Controller } from 'react-hook-form';
import { View, Text, TextInput, Button } from 'react-native';

export default function LoginForm() {
  const { control, handleSubmit, formState: { errors } } = useForm({
    defaultValues: { email: '', password: '' },
  });

  const onSubmit = (data) => console.log('Submitted', data);

  return (
    <View style={{ padding: 16, gap: 8 }}>
      <Controller
        control={control}
        name="email"
        rules={{ required: 'Email is required' }}
        render={({ field: { onChange, onBlur, value } }) => (
          <TextInput
            placeholder="Email"
            value={value}
            onChangeText={onChange}
            onBlur={onBlur}
            autoCapitalize="none"
            style={{ borderWidth: 1, padding: 10, borderRadius: 8 }}
          />
        )}
      />
      {errors.email && <Text style={{ color: 'red' }}>{errors.email.message}</Text>}

      <Controller
        control={control}
        name="password"
        rules={{ required: 'Password is required', minLength: { value: 6, message: 'At least 6 characters' } }}
        render={({ field: { onChange, onBlur, value } }) => (
          <TextInput
            placeholder="Password"
            secureTextEntry
            value={value}
            onChangeText={onChange}
            onBlur={onBlur}
            style={{ borderWidth: 1, padding: 10, borderRadius: 8 }}
          />
        )}
      />
      {errors.password && <Text style={{ color: 'red' }}>{errors.password.message}</Text>}

      <Button title="Log in" onPress={handleSubmit(onSubmit)} />
    </View>
  );
}
```

**Formik** is another option with a similar goal. Choose one library per project.

---

## 6.3 Validation

Validation checks that input is correct **before** it is used. Always validate on the server as well, since client checks can be bypassed.

When to validate:

- **On submit** — simplest and least annoying.
- **On blur** — after the user leaves a field.
- **On change** — for live feedback such as password strength.

**Schema validation with Zod**

A schema describes the allowed shape of your data in one place and can be reused for types and validation.

```bash
npm install zod @hookform/resolvers
```

```jsx
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';
import { useForm } from 'react-hook-form';

const schema = z.object({
  email: z.string().email('Enter a valid email'),
  password: z.string().min(8, 'At least 8 characters'),
  age: z.coerce.number().min(18, 'Must be 18 or older'),
});

const { control, handleSubmit, formState: { errors } } = useForm({
  resolver: zodResolver(schema),
  defaultValues: { email: '', password: '', age: '' },
});
```

Tips for good validation UX:

- Show error messages next to the field, in clear language.
- Do not block the user while they are still typing.
- Disable or show a loading state on the submit button while sending.
- Do not rely on color alone to show errors. Add text or icons for accessibility.

---

## 6.4 Gestures

Beyond taps, users swipe, drag, pinch, and long press.

**Built-in options**

- `Pressable` supports `onPress`, `onLongPress`, `onPressIn`, and `onPressOut`.
- `ScrollView` and `FlatList` handle scrolling gestures natively.
- `PanResponder` is an older low-level API for drag gestures. It works but is rarely used in new code.

**React Native Gesture Handler (recommended)**

This library runs gestures on the native side, so they stay smooth even when the JavaScript thread is busy. It is covered in more detail in [Lesson 11](./[11]-Animations-And-Gestures.md).

```jsx
import { Gesture, GestureDetector, GestureHandlerRootView } from 'react-native-gesture-handler';

export default function DoubleTapBox() {
  const doubleTap = Gesture.Tap()
    .numberOfTaps(2)
    .onEnd(() => {
      console.log('Double tapped!');
    });

  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <GestureDetector gesture={doubleTap}>
        <View style={{ width: 120, height: 120, backgroundColor: 'tomato' }} />
      </GestureDetector>
    </GestureHandlerRootView>
  );
}
```

Common gestures: `Tap`, `LongPress`, `Pan`, `Pinch`, `Rotation`, and `Fling`.

---

[Previous](./[5]-Navigation-With-React-Navigation.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[7]-Networking-And-APIs.md)