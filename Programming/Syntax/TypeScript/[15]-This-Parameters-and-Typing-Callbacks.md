[Previous](./[14]-Function-Overloads.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[16]-Interfaces.md)

*Functions*

# Lesson 15 - `this` Parameters And Typing Callbacks

## 15.1 Why `this` Needs Special Handling

In JavaScript, what `this` refers to inside a function depends entirely on *how* the function is called, not where it's defined — a frequent source of bugs. TypeScript lets you explicitly declare what `this` is expected to be, so misuse is caught at compile time.

```typescript
function reportSize(this: { width: number; height: number }) {
  console.log(this.width * this.height);
}
```

---

## 15.2 The `this` Parameter

A `this` parameter is written first in the parameter list, but it's not a real runtime parameter — TypeScript strips it out during compilation. It exists purely to describe the required `this` context:

```typescript
interface Button {
  label: string;
  onClick: (this: Button, event: MouseEvent) => void;
}

const button: Button = {
  label: "Save",
  onClick: function (event) {
    console.log(this.label); // `this` correctly typed as Button
  },
};
```

Calling `button.onClick` normally provides the right `this`, but if the function were detached and called on its own, TypeScript would flag the mismatch.

---

## 15.3 Arrow Functions and `this`

Arrow functions don't have their own `this` — they capture it from the surrounding scope at the point they're defined. This makes them the safest choice for callbacks inside classes:

```typescript
class Counter {
  count = 0;

  increment = () => {
    this.count++; // `this` always refers to the Counter instance
  };
}

const counter = new Counter();
const handler = counter.increment;
handler(); // still works correctly, unlike a regular method would
```

A regular (non-arrow) method assigned this way would lose its `this` binding when called detached from the instance — arrow functions sidestep the problem entirely.

---

## 15.4 Typing Callback Parameters

When a function accepts another function as an argument, describe its full signature — including `this`, if relevant — using a function type:

```typescript
function onEvent(callback: (data: string, index: number) => void) {
  callback("hello", 0);
}
```

TypeScript then uses contextual typing (Lesson 9) to infer the types of `data` and `index` inside any callback passed to `onEvent`, without needing to annotate them again at the call site.

---

## 15.5 `noImplicitThis`

Under `strict` mode (Lesson 52), the `noImplicitThis` flag requires `this` to have a known type wherever it's used in a plain function — it can't silently fall back to `any`. This catches accidental use of `this` in contexts (like standalone functions or arrow functions in odd places) where its value would actually be `undefined` at runtime.

```typescript
function standalone() {
  console.log(this.value); // Error: 'this' implicitly has type 'any'.
}
```

---

[Previous](./[14]-Function-Overloads.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[16]-Interfaces.md)
