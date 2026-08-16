[Previous](./[15]-Destructuring-Spread-and-Rest.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[17]-JSON.md)

*Data Structures*

# Lesson 16 - Sets And Maps

## 16.1 What Is A Set?

A **Set** is a collection of *unique* values — duplicates are automatically ignored:

```js
const ids = new Set([1, 2, 2, 3, 3, 3]);
console.log(ids); // Set(3) { 1, 2, 3 }

ids.add(4);
ids.add(1);        // ignored — already present
ids.has(2);         // true
ids.delete(3);      // removes 3
ids.size;            // 3
```

A common practical use is de-duplicating an array:

```js
const nums = [1, 2, 2, 3, 3, 3, 4];
const unique = [...new Set(nums)];
console.log(unique); // [1, 2, 3, 4]
```

---

## 16.2 Iterating A Set

```js
const colors = new Set(["red", "green", "blue"]);

for (const color of colors) {
  console.log(color);
}

colors.forEach(color => console.log(color));
```

Sets maintain insertion order, and (unlike plain objects) can hold any value as a member, including objects and `NaN`.

---

## 16.3 What Is A Map?

A **Map** is a key-value collection, similar to an object, but with a few important advantages: keys can be *any type* (not just strings), and it preserves insertion order reliably.

```js
const scores = new Map();

scores.set("Alice", 90);
scores.set("Bob", 85);

scores.get("Alice"); // 90
scores.has("Bob");    // true
scores.delete("Bob");
scores.size;           // 1
```

You can also initialize a Map directly from an array of `[key, value]` pairs:

```js
const inventory = new Map([
  ["apples", 50],
  ["bananas", 30],
]);
```

---

## 16.4 Iterating A Map

```js
const scores = new Map([
  ["Alice", 90],
  ["Bob", 85],
]);

for (const [name, score] of scores) {
  console.log(`${name}: ${score}`);
}

scores.forEach((score, name) => {
  console.log(`${name}: ${score}`);
});
```

---

## 16.5 Map vs. Plain Object

| | Object | Map |
|---|---|---|
| Key types | strings/symbols only | any value |
| Key order | mostly insertion, with quirks | always insertion order |
| Size | `Object.keys(obj).length` | `.size` |
| Iteration | needs `Object.entries()` | directly iterable |
| Best for | fixed, known structure (like a record) | dynamic collections that grow/shrink |

Use a plain object for something record-like (`{ name, age, email }`); use a Map when you're building a lookup table that changes size at runtime, especially with non-string keys.

---

## 16.6 WeakSet And WeakMap

`WeakSet` and `WeakMap` behave like their regular counterparts but only accept **objects** as members/keys, and hold those references "weakly" — meaning if nothing else references that object, JavaScript's garbage collector can remove it automatically, even while it's still inside the WeakMap/WeakSet.

```js
let user = { name: "Tomo" };
const cache = new WeakMap();

cache.set(user, "some cached data");
console.log(cache.get(user)); // "some cached data"

user = null; // the object can now be garbage collected,
             // and its entry is automatically removed from the cache
```

This makes them useful for caching or attaching metadata to objects without causing memory leaks. They can't be iterated (no `for...of`, no `.size`), which is a deliberate trade-off in exchange for that automatic cleanup. Lesson 34 covers garbage collection in more depth.

[Previous](./[15]-Destructuring-Spread-and-Rest.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[17]-JSON.md)
