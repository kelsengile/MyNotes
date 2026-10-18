[Previous](./[11]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[13]-Local-Storage.md)

*Data And Platform Features*

# Lesson 12 - Networking And APIs

Most apps talk to a server. This lesson covers making HTTP requests, converting JSON to Dart objects, showing async results in the UI, and handling failures.

---

## 12.1 The http and dio packages

**`http`** is the simple, official-style package. Add it with `flutter pub add http`.

```dart
import 'dart:convert';
import 'package:http/http.dart' as http;

Future<void> loadPost() async {
  final uri = Uri.parse('https://jsonplaceholder.typicode.com/posts/1');
  final response = await http.get(uri);

  if (response.statusCode == 200) {
    final data = jsonDecode(response.body) as Map<String, dynamic>;
    print(data['title']);
  } else {
    throw Exception('Request failed: ${response.statusCode}');
  }
}

Future<void> createPost() async {
  final response = await http.post(
    Uri.parse('https://jsonplaceholder.typicode.com/posts'),
    headers: {'Content-Type': 'application/json'},
    body: jsonEncode({'title': 'Hello', 'body': 'World', 'userId': 1}),
  );
  print(response.statusCode); // 201 means created
}
```

Other verbs: `http.put`, `http.patch`, `http.delete`. Always build URLs with `Uri.parse` or `Uri.https(host, path, queryParams)`.

**`dio`** is a more feature-rich client. Add it with `flutter pub add dio`.

```dart
import 'package:dio/dio.dart';

final dio = Dio(BaseOptions(
  baseUrl: 'https://jsonplaceholder.typicode.com',
  connectTimeout: const Duration(seconds: 10),
  receiveTimeout: const Duration(seconds: 10),
  headers: {'Accept': 'application/json'},
));

final res = await dio.get('/posts/1');
print(res.data['title']);   // dio decodes JSON for you
```

dio adds **interceptors** (attach auth tokens, log requests, retry), timeouts, cancellation, file upload/download progress, and form data.

| Need | Pick |
|---|---|
| Simple calls, minimal dependencies | `http` |
| Interceptors, timeouts, uploads, cancellation | `dio` |

**Platform notes:**

- Android needs the internet permission in release builds: `<uses-permission android:name="android.permission.INTERNET" />` in `AndroidManifest.xml`.
- On the **macOS** target, enable the "Outgoing Connections (Client)" entitlement.
- Browsers enforce **CORS** on web builds, so your server must allow your origin.
- Use `https` and never hard-code secret API keys in your app, because anything shipped in the app can be extracted.

---

## 12.2 JSON serialization

`jsonDecode` returns loosely typed `Map<String, dynamic>`. Convert it to a **model class** so the rest of your code is type-safe.

**Manual approach**, best for small models:

```dart
class Post {
  final int id;
  final String title;
  final String? body;

  const Post({required this.id, required this.title, this.body});

  factory Post.fromJson(Map<String, dynamic> json) {
    return Post(
      id: json['id'] as int,
      title: json['title'] as String,
      body: json['body'] as String?,
    );
  }

  Map<String, dynamic> toJson() => {
        'id': id,
        'title': title,
        'body': body,
      };
}
```

Parsing a list:

```dart
final List<dynamic> raw = jsonDecode(response.body);
final posts = raw
    .map((e) => Post.fromJson(e as Map<String, dynamic>))
    .toList();
```

**Nested objects** call the inner `fromJson`:

```dart
factory User.fromJson(Map<String, dynamic> json) => User(
      name: json['name'] as String,
      address: Address.fromJson(json['address'] as Map<String, dynamic>),
    );
```

**Code generation** for larger apps removes the boilerplate. With `json_serializable`:

```dart
import 'package:json_annotation/json_annotation.dart';
part 'post.g.dart';

@JsonSerializable()
class Post {
  final int id;
  final String title;

  Post({required this.id, required this.title});

  factory Post.fromJson(Map<String, dynamic> json) => _$PostFromJson(json);
  Map<String, dynamic> toJson() => _$PostToJson(this);
}
```

Install: `flutter pub add json_annotation` and `flutter pub add --dev build_runner json_serializable`, then generate:

```bash
dart run build_runner build --delete-conflicting-outputs
```

Packages like `freezed` add immutability and `copyWith` on top.

---

## 12.3 FutureBuilder and async UI

`FutureBuilder` builds UI from a `Future`, with separate states for loading, error, and data.

```dart
class PostsPage extends StatefulWidget {
  const PostsPage({super.key});
  @override
  State<PostsPage> createState() => _PostsPageState();
}

class _PostsPageState extends State<PostsPage> {
  late Future<List<Post>> _future;

  @override
  void initState() {
    super.initState();
    _future = fetchPosts();     // create the Future ONCE
  }

  @override
  Widget build(BuildContext context) {
    return FutureBuilder<List<Post>>(
      future: _future,
      builder: (context, snapshot) {
        if (snapshot.connectionState != ConnectionState.done) {
          return const Center(child: CircularProgressIndicator());
        }
        if (snapshot.hasError) {
          return Center(child: Text('Error: ${snapshot.error}'));
        }
        final posts = snapshot.data!;
        return ListView.builder(
          itemCount: posts.length,
          itemBuilder: (_, i) => ListTile(title: Text(posts[i].title)),
        );
      },
    );
  }
}
```

**Critical mistake to avoid:** never call `fetchPosts()` directly inside `build` as the `future:` argument. `build` runs often, so the request would repeat on every rebuild. Store the Future in `initState` (as above) or in state management.

To refresh, assign a new future inside `setState`:

```dart
RefreshIndicator(
  onRefresh: () async {
    setState(() => _future = fetchPosts());
    await _future;
  },
  child: /* a scrollable */,
)
```

For continuously updating data (WebSockets, Firebase), use **`StreamBuilder`**, which works the same way with a `Stream`. With Riverpod, `FutureProvider` and `.when(...)` replace this boilerplate (Lesson 11).

---

## 12.4 Error handling

Things go wrong: no connection, timeouts, server errors, bad data. Handle each deliberately.

**1. Check the status code** and map failures to meaningful errors:

```dart
class ApiException implements Exception {
  final String message;
  final int? statusCode;
  ApiException(this.message, [this.statusCode]);

  @override
  String toString() => message;
}

Future<List<Post>> fetchPosts() async {
  try {
    final response = await http
        .get(Uri.parse('https://jsonplaceholder.typicode.com/posts'))
        .timeout(const Duration(seconds: 10));

    if (response.statusCode == 200) {
      final list = jsonDecode(response.body) as List<dynamic>;
      return list
          .map((e) => Post.fromJson(e as Map<String, dynamic>))
          .toList();
    }
    if (response.statusCode == 401) {
      throw ApiException('Please sign in again', 401);
    }
    throw ApiException('Server error', response.statusCode);
  } on TimeoutException {
    throw ApiException('The request timed out');
  } on SocketException {
    throw ApiException('No internet connection');
  } on FormatException {
    throw ApiException('Received invalid data');
  }
}
```

(`TimeoutException` needs `import 'dart:async';` and `SocketException` needs `import 'dart:io';`, which is not available on the web, so catch a more general exception there.)

**With dio,** catch `DioException` and inspect `e.type` (such as `connectionTimeout`, `badResponse`) and `e.response?.statusCode`.

**2. Show errors helpfully.** Provide a message and a **Retry** button rather than a blank screen:

```dart
Column(
  mainAxisSize: MainAxisSize.min,
  children: [
    Text(error.toString()),
    FilledButton(onPressed: _reload, child: const Text('Retry')),
  ],
)
```

**3. Good practices:**

- Keep API code in a **repository or service class** so widgets never call `http` directly.
- Always set **timeouts**.
- Never show raw exception text with technical details to end users.
- Consider **retry with backoff** for transient failures.
- Log unexpected errors to a crash reporting service (Lesson 16).
- Cache data locally (Lesson 13) so the app still works offline.

---

[Previous](./[11]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Flutter.md) | [Next](./[13]-Local-Storage.md)