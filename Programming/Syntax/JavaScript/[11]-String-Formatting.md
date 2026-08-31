[Previous](./%5B10%5D-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B12%5D-Error-Handling.md)

*Core Syntax*

# Lesson 11 - String Formatting

## 11.1 Template Literals

Introduced briefly in Lesson 6, **template literals** (backtick strings) let you embed expressions and write multi-line text directly:

```js
const name = "Priya";
const age = 28;

const message = `${name} is ${age} years old.
Next year she'll be ${age + 1}.`;

console.log(message);
```

Any valid expression can go inside `${ }` — variables, math, function calls, even ternaries.

---

## 11.2 Common String Methods

Strings come with many built-in methods for reading and transforming them:

```js
const text = "  Hello, World!  ";

text.length;              // 18
text.trim();                // "Hello, World!"        (removes whitespace from both ends)
text.toUpperCase();        // "  HELLO, WORLD!  "
text.toLowerCase();        // "  hello, world!  "
text.includes("World");   // true
text.startsWith("  He"); // true
text.endsWith("!  ");     // true
text.indexOf("World");   // 9
text.replace("World", "There"); // "  Hello, There!  "
text.replaceAll("l", "L"); // replaces every "l"
```

Strings are **immutable** — every method above returns a *new* string rather than modifying the original.

---

## 11.3 Slicing And Splitting

```js
const text = "Hello, World!";

text.slice(0, 5);     // "Hello"
text.slice(7);         // "World!"
text.slice(-6);        // "World!"  (negative counts from the end)

const csv = "apple,banana,cherry";
csv.split(",");         // ["apple", "banana", "cherry"]

const chars = "hi".split(""); // ["h", "i"]
```

`split()` is especially useful for turning delimited data (like a CSV line) into an array you can loop over with the tools from Lesson 13.

---

## 11.4 Joining Strings

Besides template literals, strings can be combined with `+` or joined from an array:

```js
"Hello, " + "World!";         // "Hello, World!"

["Hello", "World"].join(" "); // "Hello World"
["a", "b", "c"].join("-");     // "a-b-c"
```

Template literals are generally preferred for readability once more than one or two values are involved.

---

## 11.5 Regex Basics

A **regular expression** (regex) is a pattern used to match text. JavaScript strings have a few methods that accept regex for more flexible matching than plain substring checks:

```js
const text = "Contact: 555-123-4567";

const pattern = /\d{3}-\d{3}-\d{4}/; // matches a phone-number-shaped pattern
pattern.test(text);      // true — does the pattern exist in the string?

text.match(pattern);     // ["555-123-4567"] — returns the actual match(es)

text.replace(/\d/g, "#"); // "Contact: ###-###-####" — the g flag replaces all matches
```

Common pattern building blocks: `\d` (a digit), `\w` (a word character), `\s` (whitespace), `+` (one or more), `*` (zero or more), `{n}` (exactly n times). Regex is a deep topic on its own — Lesson 44 covers it fully; this is just enough to recognize it when you see it.

[Previous](./%5B10%5D-Functions-and-Scope%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B12%5D-Error-Handling%20%281%29.md)
