[Previous](./[12]-Typing-Functions.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[14]-Function-Overloads.md)

*Functions*

# Lesson 13 - Optional, Default And Rest Parameters

## 13.1 Optional Parameters

A parameter marked with `?` may be left out entirely when the function is called:

```typescript
function greet(name: string, title?: string) {
  return title ? `Hello, ${title} ${name}` : `Hello, ${name}`;
}

greet("Amy");          // OK
greet("Amy", "Dr.");   // OK
```

Inside the function body, `title` has the type `string | undefined` — so you'll need to check for its presence before using it as a plain string, following the same pattern as optional object properties from Lesson 7.

Optional parameters must come **after** all required parameters in the list.

---

## 13.2 Default Parameters

A default value can be given directly in the parameter list, and is used whenever the argument is omitted or explicitly `undefined`:

```typescript
function greet(name: string, greeting: string = "Hello") {
  return `${greeting}, ${name}`;
}

greet("Amy");            // "Hello, Amy"
greet("Amy", "Hi");      // "Hi, Amy"
```

Unlike a plain optional parameter, the type of `greeting` inside the function is just `string` — never `undefined` — since a default always fills that gap. TypeScript also infers the parameter's type from the default value if no annotation is given.

---

## 13.3 Rest Parameters

A rest parameter collects any number of remaining arguments into a single typed array, using `...`:

```typescript
function sum(...numbers: number[]): number {
  return numbers.reduce((total, n) => total + n, 0);
}

sum(1, 2, 3, 4); // 10
```

A rest parameter must be the **last** parameter in the list, and there can only be one per function.

---

## 13.4 Combining All Three

Required, optional/default, and rest parameters can be mixed in one function, as long as the ordering rules are respected:

```typescript
function createUser(
  name: string,
  role: string = "member",
  ...permissions: string[]
) {
  return { name, role, permissions };
}

createUser("Amy");
createUser("Amy", "admin", "read", "write", "delete");
```

---

## 13.5 Destructured Parameters with Defaults

Object destructuring in parameters can also carry its own type annotation and defaults, which is common for functions that take a single "options" object:

```typescript
function createWindow({
  width = 800,
  height = 600,
  title,
}: { width?: number; height?: number; title: string }) {
  console.log(`${title}: ${width}x${height}`);
}

createWindow({ title: "Editor" }); // width/height fall back to their defaults
```

This pattern keeps call sites readable, especially once a function grows past two or three parameters.

---

[Previous](./[12]-Typing-Functions.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[14]-Function-Overloads.md)
