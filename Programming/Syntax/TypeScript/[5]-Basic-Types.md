[Previous](./[4]-TypeScript-in-the-Editor.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[6]-Arrays-and-Tuples.md)

*Core Types*

# Lesson 5 - Basic Types (string, Number, Boolean, Null, Undefined)

## 5.1 Annotating Variables

TypeScript lets you declare the type of a variable explicitly using a colon after its name:

```typescript
let username: string = "amy";
let age: number = 29;
let isActive: boolean = true;
```

Once a variable has a type, TypeScript will flag any attempt to assign a value of a different type to it.

---

## 5.2 `string`, `number`, and `boolean`

These are the three most common primitive types:

```typescript
let city: string = "Manila";
let temperature: number = 31.5;
let isRaining: boolean = false;
```

Note that TypeScript's `number` type covers all numbers — integers and floats alike; there's no separate `int` or `float` type as in some other languages.

---

## 5.3 `null` and `undefined`

Both `null` and `undefined` are valid types on their own:

```typescript
let empty: null = null;
let notSet: undefined = undefined;
```

By default, under `strictNullChecks` (part of `strict` mode — see Lesson 52), `null` and `undefined` are **not** automatically assignable to other types like `string` or `number`. This is intentional: it forces you to explicitly handle the "value might be missing" case, which is one of TypeScript's biggest wins for avoiding runtime crashes.

```typescript
let name: string = null; // Error with strictNullChecks: Type 'null' is not assignable to type 'string'.
let name2: string | null = null; // OK — see Lesson 22 for union types
```

---

## 5.4 Letting TypeScript Infer Basic Types

You rarely need to annotate every variable — TypeScript infers the type from the assigned value automatically:

```typescript
let score = 100;       // inferred as number
let label = "Level 1"; // inferred as string
```

This is called **type inference**, covered in depth in Lesson 9. As a general style rule, prefer letting TypeScript infer types when the value makes it obvious, and only add explicit annotations when it improves clarity or when there's no initial value to infer from.

---

## 5.5 `bigint` and `symbol`

Two less common but still built-in primitives:

```typescript
let big: bigint = 9007199254740993n; // for integers beyond Number's safe range
let sym: symbol = Symbol("id");      // guaranteed-unique values, often used as object keys
```

These show up less frequently in everyday code but are part of TypeScript's full primitive type set, mirroring JavaScript itself.

---

[Previous](./[4]-TypeScript-in-the-Editor.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[6]-Arrays-and-Tuples.md)
