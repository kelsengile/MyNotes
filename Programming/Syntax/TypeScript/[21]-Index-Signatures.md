[Previous](./[20]-Readonly-and-Optional-Properties.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[22]-Union-and-Intersection-Types.md)

*Interfaces & Type Aliases*

# Lesson 21 - Index Signatures

## 21.1 The Problem: Unknown Property Names

Sometimes an object's exact property names aren't known ahead of time, but their *type* is consistent — for example, a dictionary mapping arbitrary usernames to scores. An index signature describes this pattern:

```typescript
interface Scores {
  [username: string]: number;
}

const scores: Scores = {
  amy: 95,
  ben: 88,
};

scores.cid = 72; // OK — any string key works, as long as the value is a number
```

The name `username` inside the brackets is just a label for readability — it doesn't restrict which strings are valid keys.

---

## 21.2 String vs. Number Index Signatures

Index signatures can be keyed by `string` or `number`:

```typescript
interface StringDictionary {
  [key: string]: string;
}

interface NumberedList {
  [index: number]: string;
}

const list: NumberedList = ["a", "b", "c"]; // arrays already satisfy this shape
```

A numeric index signature is how TypeScript itself types built-in arrays and array-like structures under the hood.

---

## 21.3 Combining an Index Signature with Known Properties

You can mix specific, named properties with a general index signature, as long as the named properties' types are compatible with the index signature's type:

```typescript
interface Config {
  name: string;         // specific, required
  [key: string]: string; // general fallback — must also be assignable to string
}

const config: Config = { name: "app", env: "production", version: "1.0" };
```

If `name` were typed as `number` instead, this would be a compile error — every named property must fit within what the index signature promises for any string key.

---

## 21.4 Index Signatures with `Record`

The built-in `Record<K, V>` utility type is a more concise way to express the same idea, and is generally preferred for simple key-value maps:

```typescript
type Scores = Record<string, number>;

const scores: Scores = { amy: 95, ben: 88 };
```

`Record` also supports a union of specific string literals as keys, which produces a more precise, closed set of required properties — something a raw index signature cannot do:

```typescript
type Theme = Record<"light" | "dark", string>;

const theme: Theme = { light: "#fff", dark: "#000" }; // both keys required
```

`Record` is covered further among the other built-in utility types in Lesson 39.

---

## 21.5 A Caveat: No Guarantee the Key Exists

Index signatures tell TypeScript "any key of this type maps to this value type" — but they don't guarantee any particular key is actually present at runtime:

```typescript
const scores: Record<string, number> = {};
console.log(scores.amy.toFixed(2)); // Runtime error: Cannot read properties of undefined
```

Enabling the `noUncheckedIndexedAccess` compiler flag makes TypeScript reflect this honestly, typing indexed access as `number | undefined` instead of just `number`, forcing an explicit check before use.

---

[Previous](./[20]-Readonly-and-Optional-Properties.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[22]-Union-and-Intersection-Types.md)
