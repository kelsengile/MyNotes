[Previous](./[19]-Making-Network-Requests.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[21]-Async-Programming.md)

*Networking*

# Lesson 20 - Working with JSON

## 20.1 What is JSON?

**JSON** (JavaScript Object Notation) is the standard text format servers use to send structured data to mobile apps. It represents data as nested objects (`{}`) and arrays (`[]`) of key-value pairs:

```json
{
  "id": 42,
  "name": "Alex",
  "tags": ["admin", "beta-tester"],
  "address": { "city": "Austin", "zip": "78701" }
}
```

---

## 20.2 Parsing JSON into Objects

Raw JSON arrives as text and must be **decoded** into your app's own model classes before you can use it safely and with type-checking:

```dart
class User {
  final int id;
  final String name;
  User({required this.id, required this.name});

  factory User.fromJson(Map<String, dynamic> json) {
    return User(id: json['id'], name: json['name']);
  }
}

final user = User.fromJson(jsonDecode(response.body));
```

```swift
struct User: Codable {
    let id: Int
    let name: String
}
let user = try JSONDecoder().decode(User.self, from: data)
```

Swift's `Codable` and Kotlin's `kotlinx.serialization` can generate this parsing logic automatically from your model's property declarations, avoiding tedious hand-written parsing code.

---

## 20.3 Encoding Objects Back to JSON

The reverse direction matters too — when sending data to a server (e.g., submitting a form), your model object is **encoded** back into a JSON string:

```dart
final json = jsonEncode({'name': nameController.text, 'email': emailController.text});
```

---

## 20.4 Handling Optional and Unexpected Fields

Real-world APIs are messy: fields can be missing, `null`, or of an unexpected type. Robust JSON parsing treats every field as potentially absent (using optional types from Lesson 4) and provides sensible defaults, rather than assuming the response always matches the expected shape exactly — a crash-prone assumption that breaks the moment the backend team changes something.

[Previous](./[19]-Making-Network-Requests.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[21]-Async-Programming.md)
