[Previous](./[8]-Any-Unknown-Void-and-Never.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[10]-Type-Assertions.md)

*Core Types*

# Lesson 9 - Type Inference And Contextual Typing

## 9.1 What Type Inference Is

TypeScript doesn't require an annotation on every single variable — it can often figure out the type on its own, based on the assigned value:

```typescript
let count = 5;          // inferred: number
let title = "Report";   // inferred: string
let flags = [true, false]; // inferred: boolean[]
```

Hovering over `count` in an editor shows `let count: number`, even though no annotation was written. This is TypeScript quietly doing work on your behalf.

---

## 9.2 Inference in Function Return Types

Return types are inferred from a function's body, unless explicitly annotated:

```typescript
function double(n: number) {
  return n * 2; // return type inferred as number
}
```

Explicit return type annotations are still good practice for public/exported functions — they act as a safety net, catching accidental changes to a function's behavior, and they make the function's contract clear without needing to read its implementation.

---

## 9.3 Best Common Type

When inferring the type of an array with mixed-looking elements, TypeScript looks for a type that fits all of them:

```typescript
let items = [1, 2, "three"]; // inferred: (string | number)[]
```

If no reasonable common type exists, TypeScript falls back to a broader type, or in the worst case, `any[]`.

---

## 9.4 Contextual Typing

Sometimes inference flows the *other* direction — from where a value is used, back onto the value itself. This is called **contextual typing**. A classic example is callback parameters:

```typescript
window.addEventListener("click", (event) => {
  console.log(event.button); // event is inferred as MouseEvent, no annotation needed
});
```

TypeScript already knows the signature of `addEventListener`, so it uses that context to infer the type of `event` inside the callback — without you writing `(event: MouseEvent) =>` yourself.

---

## 9.5 When to Annotate Anyway

Good general guidelines:

- Let inference handle simple local variables (`let x = 5`).
- Explicitly annotate function parameters — TypeScript can't infer these from nothing.
- Explicitly annotate exported/public function return types, for clarity and stability.
- Annotate variables that start `undefined`/empty and are filled in later, since there's nothing yet to infer from:

```typescript
let results: string[] = []; // without the annotation, this would infer as any[]
```

Relying on inference where it's reliable, and adding annotations where it isn't, keeps code both concise and safe.

---

[Previous](./[8]-Any-Unknown-Void-and-Never.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[10]-Type-Assertions.md)
