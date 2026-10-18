[Previous](./[6]-Styling-And-Theming.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[8]-Responsive-Design.md)

*Building The UI*

# Lesson 7 - Forms And User Input

Apps need to collect text, validate it, and respond to taps and gestures. This lesson covers text fields, forms with validation, touch handling, and the on-screen keyboard.

---

## 7.1 TextField and controllers

A basic text field with a decoration:

```dart
const TextField(
  decoration: InputDecoration(
    labelText: 'Email',
    hintText: 'you@example.com',
    prefixIcon: Icon(Icons.email),
    border: OutlineInputBorder(),
  ),
)
```

**Reading the value** two ways:

```dart
// 1. onChanged: called on every keystroke
TextField(onChanged: (value) => debugPrint(value));

// 2. TextEditingController: read or set the text any time
class _MyFormState extends State<MyForm> {
  final _controller = TextEditingController();

  @override
  void dispose() {
    _controller.dispose(); // always dispose controllers
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextField(controller: _controller),
        ElevatedButton(
          onPressed: () => debugPrint(_controller.text),
          child: const Text('Print'),
        ),
      ],
    );
  }
}
```

Controllers belong in a `State` class, created once and disposed in `dispose()`. Never create one inside `build`.

**Common options:**

```dart
TextField(
  obscureText: true,                       // password dots
  keyboardType: TextInputType.emailAddress,
  textInputAction: TextInputAction.next,   // keyboard action button
  maxLength: 50,
  maxLines: 1,                             // use null for a growing field
  autofocus: false,
  onSubmitted: (value) {},
)
```

---

## 7.2 Form and validation

`Form` groups several `TextFormField`s so they can be validated together. A `GlobalKey<FormState>` gives you access to the form's state.

```dart
class SignupForm extends StatefulWidget {
  const SignupForm({super.key});
  @override
  State<SignupForm> createState() => _SignupFormState();
}

class _SignupFormState extends State<SignupForm> {
  final _formKey = GlobalKey<FormState>();
  final _emailCtrl = TextEditingController();
  final _passCtrl = TextEditingController();

  @override
  void dispose() {
    _emailCtrl.dispose();
    _passCtrl.dispose();
    super.dispose();
  }

  void _submit() {
    if (_formKey.currentState!.validate()) {
      debugPrint('Email: ${_emailCtrl.text}');
      // continue: call your API, navigate, etc.
    }
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: [
          TextFormField(
            controller: _emailCtrl,
            decoration: const InputDecoration(labelText: 'Email'),
            keyboardType: TextInputType.emailAddress,
            validator: (value) {
              if (value == null || value.isEmpty) return 'Email is required';
              if (!value.contains('@')) return 'Enter a valid email';
              return null; // null means valid
            },
          ),
          TextFormField(
            controller: _passCtrl,
            decoration: const InputDecoration(labelText: 'Password'),
            obscureText: true,
            validator: (value) =>
                (value == null || value.length < 8) ? 'Min 8 characters' : null,
          ),
          const SizedBox(height: 16),
          ElevatedButton(onPressed: _submit, child: const Text('Sign up')),
        ],
      ),
    );
  }
}
```

How it works:

- A `validator` returns an error message string, or `null` when the input is valid.
- `validate()` runs every validator and returns `true` only if all pass.
- Set `autovalidateMode: AutovalidateMode.onUserInteraction` on the Form to show errors as the user types after first interaction.
- `_formKey.currentState!.reset()` clears the form and `save()` triggers each field's `onSaved`.

---

## 7.3 Gestures and taps

Buttons already handle taps. To make **any** widget interactive, wrap it:

```dart
GestureDetector(
  onTap: () => debugPrint('tap'),
  onDoubleTap: () => debugPrint('double'),
  onLongPress: () => debugPrint('long press'),
  child: Container(
    width: 100,
    height: 100,
    color: Colors.orange,
  ),
)
```

`GestureDetector` has no visual feedback. For the Material ripple effect, use **`InkWell`** (it needs a `Material` ancestor, which `Scaffold` and `Card` provide):

```dart
InkWell(
  borderRadius: BorderRadius.circular(8),
  onTap: () {},
  child: const Padding(
    padding: EdgeInsets.all(12),
    child: Text('Tap me'),
  ),
)
```

**Drag and pan gestures:**

```dart
GestureDetector(
  onPanUpdate: (details) {
    debugPrint('moved by ${details.delta}');
  },
  child: const FlutterLogo(size: 80),
)
```

Other useful widgets: `Dismissible` (swipe to remove a list item), `Draggable` / `DragTarget`, and `Listener` for raw pointer events. Make sure tap targets are at least about 48x48 logical pixels for accessibility.

---

## 7.4 Keyboard handling

**Dismiss the keyboard** by removing focus:

```dart
FocusScope.of(context).unfocus();
```

A common pattern is dismissing it when the user taps outside any field:

```dart
GestureDetector(
  onTap: () => FocusScope.of(context).unfocus(),
  child: Scaffold(/* ... */),
)
```

**Move between fields** with a `FocusNode`:

```dart
final _passwordFocus = FocusNode();

TextField(
  textInputAction: TextInputAction.next,
  onSubmitted: (_) => FocusScope.of(context).requestFocus(_passwordFocus),
),
TextField(focusNode: _passwordFocus),
// remember: _passwordFocus.dispose() in dispose()
```

**Keyboard covering your content:**

- `Scaffold` resizes its body when the keyboard opens (`resizeToAvoidBottomInset` is true by default).
- If the form is taller than the remaining space, wrap it in a `SingleChildScrollView` to avoid overflow.
- You can read the keyboard height with `MediaQuery.of(context).viewInsets.bottom`.

**Pick the right keyboard** through `keyboardType`: `text`, `number`, `emailAddress`, `phone`, `url`, `multiline`. To restrict characters, use `inputFormatters`:

```dart
import 'package:flutter/services.dart';

TextField(
  keyboardType: TextInputType.number,
  inputFormatters: [FilteringTextInputFormatter.digitsOnly],
)
```

---

[Previous](./[6]-Styling-And-Theming.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[8]-Responsive-Design.md)