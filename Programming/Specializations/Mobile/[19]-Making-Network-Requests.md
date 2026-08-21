[Previous](./[18]-Working-with-Databases.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[20]-Working-with-JSON.md)

*Networking*

# Lesson 19 - Making Network Requests (REST APIs)

## 19.1 What is a REST API?

Most mobile apps fetch and send data to a **server** over the internet through a **REST API** — a set of URL endpoints that respond to standard HTTP methods:

- **GET** — retrieve data (e.g., `GET /users/42` fetches user 42).
- **POST** — create new data (e.g., `POST /orders` places an order).
- **PUT/PATCH** — update existing data.
- **DELETE** — remove data.

The server responds with a **status code** (e.g., `200` success, `404` not found, `500` server error) and usually a JSON body containing the requested data (covered in Lesson 20).

---

## 19.2 Making a Request

```dart
final response = await http.get(Uri.parse('https://api.example.com/users/42'));
if (response.statusCode == 200) {
  print(response.body);
}
```

```swift
let (data, response) = try await URLSession.shared.data(from: url)
```

Note the `await` keyword — network requests are inherently slow and asynchronous, a topic fully covered in Lesson 21. Blocking the UI thread while waiting for a server response would freeze the entire app.

---

## 19.3 Headers and Authentication

Requests often include **headers** — metadata sent alongside the request, most commonly an `Authorization` header carrying a token that proves who the user is:

```dart
final response = await http.get(
  Uri.parse(url),
  headers: {'Authorization': 'Bearer $accessToken'},
);
```

---

## 19.4 API Clients and Base Configuration

Rather than repeating the base URL and headers on every call, most apps build a small **API client** wrapper that centralizes this configuration, handles common error cases, and exposes clean methods like `fetchUser(id)` to the rest of the app. Popular libraries like `dio` (Flutter) or `Alamofire` (iOS) provide building blocks for this — interceptors, automatic retries, and request/response logging.

[Previous](./[18]-Working-with-Databases.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[20]-Working-with-JSON.md)
