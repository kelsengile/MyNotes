[Previous](./[15]-Managing-UI-State.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[17]-Local-Storage-and-Persistence.md)

*State & Data*

# Lesson 16 - Handling User Input & Forms

## 16.1 Text Input Basics

Text fields capture typed input and are usually bound to state so the current value is always available to your code as the user types:

```dart
TextField(
  onChanged: (value) => setState(() => email = value),
  decoration: InputDecoration(labelText: "Email"),
)
```

A **controller** object (`TextEditingController` in Flutter, `TextFieldValue` in Compose) is often used instead of raw `onChanged` callbacks for more control — reading, clearing, or pre-filling a field's value programmatically.

---

## 16.2 Input Types and Keyboards

Mobile keyboards adapt to the kind of data being entered — an email field shows the `@` key prominently, a numeric field shows a number pad. Specifying the right input type improves usability significantly:

```dart
TextField(keyboardType: TextInputType.emailAddress)
TextField(keyboardType: TextInputType.number)
TextField(obscureText: true) // for passwords
```

---

## 16.3 Validation

Validation checks user input against rules (required, correct format, minimum length) before allowing a form to submit, and gives clear feedback when something's wrong:

```dart
String? validateEmail(String? value) {
  if (value == null || value.isEmpty) return "Email is required";
  if (!value.contains("@")) return "Enter a valid email";
  return null; // null means valid
}
```

Good validation happens **as the user types or leaves a field** (not only on submit), so mistakes are caught early rather than in a wall of errors at the end.

---

## 16.4 Building a Complete Form

Frameworks provide a `Form` container that groups multiple inputs, tracks their validity together, and lets you validate and submit them as a unit:

```dart
final formKey = GlobalKey<FormState>();

Form(
  key: formKey,
  child: Column(children: [
    TextFormField(validator: validateEmail),
    ElevatedButton(
      onPressed: () {
        if (formKey.currentState!.validate()) {
          submitForm();
        }
      },
      child: Text("Submit"),
    ),
  ]),
)
```

[Previous](./[15]-Managing-UI-State.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[17]-Local-Storage-and-Persistence.md)
