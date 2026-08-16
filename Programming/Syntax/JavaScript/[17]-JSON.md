[Previous](./[16]-Sets-and-Maps.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[18]-OOP-Classes-and-Objects.md)

*Data Structures*

# Lesson 17 - JSON

## 17.1 What Is JSON?

**JSON** (JavaScript Object Notation) is a lightweight, text-based format for representing structured data. Despite the name, it's language-independent and used everywhere — APIs, config files, databases — precisely because nearly every programming language can read and write it.

```json
{
  "name": "Nadia",
  "age": 27,
  "isActive": true,
  "hobbies": ["reading", "cycling"],
  "address": {
    "city": "Nairobi"
  }
}
```

JSON supports only a limited set of types: objects, arrays, strings (double-quoted only), numbers, booleans, and `null`. It has no support for functions, `undefined`, `Symbol`, comments, or trailing commas — all common causes of a "invalid JSON" error.

---

## 17.2 Converting A JavaScript Value To JSON

`JSON.stringify()` converts a JavaScript value into a JSON-formatted string:

```js
const user = {
  name: "Nadia",
  age: 27,
  isActive: true,
};

const jsonString = JSON.stringify(user);
console.log(jsonString);
// '{"name":"Nadia","age":27,"isActive":true}'
console.log(typeof jsonString); // "string"
```

For readable, indented output (useful for logging or writing to a file), pass extra arguments:

```js
JSON.stringify(user, null, 2);
```
The `2` sets the indentation width in spaces. The middle argument (here `null`) can filter or transform properties — usually left as `null` for simple cases.

Note: functions, `undefined` values, and `Symbol`s are silently dropped when stringifying.

---

## 17.3 Converting JSON Back To A JavaScript Value

`JSON.parse()` does the reverse — turning a JSON string into a real JavaScript value you can work with:

```js
const jsonString = '{"name":"Nadia","age":27}';
const user = JSON.parse(jsonString);

console.log(user.name); // "Nadia"
console.log(typeof user); // "object"
```

If the string isn't valid JSON, `JSON.parse()` throws a `SyntaxError` — this is exactly the kind of risky operation that belongs in a `try/catch` block, as shown in Lesson 12.

```js
try {
  JSON.parse("{ not valid json");
} catch (error) {
  console.log("Invalid JSON:", error.message);
}
```

---

## 17.4 Deep Cloning With JSON

A common (if imperfect) trick for deep-copying a plain object or array is round-tripping it through JSON:

```js
const original = { name: "Nadia", hobbies: ["reading", "cycling"] };
const clone = JSON.parse(JSON.stringify(original));

clone.hobbies.push("swimming");
console.log(original.hobbies); // ["reading", "cycling"] — untouched
console.log(clone.hobbies);    // ["reading", "cycling", "swimming"]
```

This works only for JSON-safe data — it silently drops functions, loses `Date` objects (they become strings), and fails on circular references. For anything beyond simple data, use `structuredClone()` (a built-in, more capable deep-clone function) instead.

---

## 17.5 Where JSON Is Used

JSON is the standard format for data exchanged between a browser/app and a server. When you make an HTTP request (Lesson 47) to an API, the response body is typically JSON text, which you parse into a usable JavaScript object:

```js
// A preview of what's covered fully in Lesson 47:
const response = await fetch("https://api.example.com/users/1");
const user = await response.json(); // parses the JSON response body automatically
console.log(user.name);
```

`package.json` (Lesson 4) and countless configuration files across the JavaScript ecosystem also use this same format.

[Previous](./[16]-Sets-and-Maps.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[18]-OOP-Classes-and-Objects.md)
