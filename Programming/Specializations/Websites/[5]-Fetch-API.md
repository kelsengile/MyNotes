[Previous](./[4]-Asynchronous-JavaScript.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[6]-ES-Modules.md)

*Intermediate JavaScript*

# Lesson 5 - Fetching Data with the Fetch API

## 5.1 Making a Basic Request

The `fetch()` function is the browser's built-in way to make HTTP requests. It returns a Promise that resolves to a `Response` object once headers arrive (the body may still be streaming):

```js
const response = await fetch("https://api.example.com/users");
const data = await response.json();
console.log(data);
```

Note the two `await` steps: one for the response itself, and a second to parse its body (`.json()`, `.text()`, `.blob()`, etc., which are themselves asynchronous).

---

## 5.2 Checking for Errors

`fetch()` only rejects on network failure (e.g. no connection) — it does **not** reject on HTTP error status codes like `404` or `500`. You must check `response.ok` or `response.status` yourself:

```js
const response = await fetch(url);
if (!response.ok) {
  throw new Error(`Request failed: ${response.status}`);
}
```

---

## 5.3 Sending Data with POST

By default `fetch()` sends a `GET` request. To send data, pass an options object as the second argument:

```js
await fetch("https://api.example.com/users", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ name: "Ada" })
});
```

The `Content-Type` header tells the server how to interpret the body, and `JSON.stringify` converts a JavaScript object into a JSON string for transmission.

---

## 5.4 Handling Failures Gracefully

Wrap fetch calls in `try/catch` so a dropped connection doesn't crash the app:

```js
try {
  const response = await fetch(url);
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  const data = await response.json();
} catch (error) {
  console.error("Fetch failed:", error);
}
```

---

## 5.5 CORS in Brief

**CORS (Cross-Origin Resource Sharing)** is a browser security mechanism that blocks a page from freely reading responses from a different origin (different domain, protocol, or port) unless the server explicitly allows it via response headers. This is why calling your own backend usually works fine, but calling a random third-party API from the browser can fail with a CORS error unless that API opts in. CORS is covered in more depth in Lesson 36.

---

[Previous](./[4]-Asynchronous-JavaScript.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[6]-ES-Modules.md)
