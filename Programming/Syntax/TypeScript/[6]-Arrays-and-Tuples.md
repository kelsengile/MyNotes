[Previous](./[5]-Basic-Types.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[7]-Objects-and-Type-Literals.md)

*Core Types*

# Lesson 6 - Arrays And Tuples

## 6.1 Typing Arrays

An array's type describes what kind of elements it holds, written with square brackets after the element type:

```typescript
let scores: number[] = [95, 88, 72];
let names: string[] = ["Ana", "Beth", "Cid"];
```

An equivalent generic syntax, `Array<T>`, means the same thing:

```typescript
let scores2: Array<number> = [95, 88, 72];
```

Both forms are common; `T[]` is generally preferred for simple types, while `Array<T>` can read more clearly for complex element types.

---

## 6.2 Arrays of Complex Types

Array element types aren't limited to primitives — they can be objects, unions, or other arrays:

```typescript
let matrix: number[][] = [
  [1, 2, 3],
  [4, 5, 6],
];

let ids: (string | number)[] = ["a1", 2, "c3"]; // union type, see Lesson 22
```

---

## 6.3 Readonly Arrays

Prefixing an array type with `readonly` (or using `ReadonlyArray<T>`) prevents mutation:

```typescript
let fixedList: readonly string[] = ["a", "b", "c"];

fixedList.push("d");  // Error: Property 'push' does not exist on type 'readonly string[]'.
fixedList[0] = "z";   // Error: Index signature in type 'readonly string[]' only permits reading.
```

This is useful for signaling "this array should never be modified after creation," and pairs with the broader `readonly` modifier covered in Lesson 20.

---

## 6.4 What Tuples Are

A **tuple** is a fixed-length array where each position has its own specific, known type. Unlike a regular array, order and count matter:

```typescript
let point: [number, number] = [10, 20];
let user: [string, number, boolean] = ["Amy", 29, true];
```

Accessing `point[0]` is guaranteed to be a `number`, and `point[1]` is guaranteed to be a `number` too — but attempting to add a third element, or assigning the wrong type to a position, is an error:

```typescript
point = [10, 20, 30];   // Error: too many elements
point = [10, "20"];     // Error: Type 'string' is not assignable to type 'number'.
```

---

## 6.5 Named Tuples and Optional Elements

Tuple positions can be labeled for readability (labels are purely documentation — they don't change the type) and can be marked optional with `?`:

```typescript
let range: [start: number, end: number] = [0, 100];
let rgba: [red: number, green: number, blue: number, alpha?: number] = [255, 0, 0];
```

Tuples are especially common as return types for functions that produce a small, fixed set of related values — for example, a `useState`-style pair: `[value: T, setValue: (v: T) => void]`.

---

[Previous](./[5]-Basic-Types.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[7]-Objects-and-Type-Literals.md)
