[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[9]-Loops.md)

*Core Syntax*

# Lesson 8 - Conditionals

## 8.1 if, else if, else

Conditionals let a program make decisions and run different code depending on whether something is true:

```js
let temperature = 15;

if (temperature > 30) {
  console.log("It's hot");
} else if (temperature > 15) {
  console.log("It's warm");
} else {
  console.log("It's cold");
}
```

JavaScript checks each condition top to bottom and runs the first block whose condition is `true`, skipping the rest. `else` runs only when none of the conditions above it matched.

---

## 8.2 Truthy And Falsy In Conditions

Any value can be used as a condition, not just booleans — JavaScript coerces it to `true` or `false` behind the scenes (see Lesson 6's coverage of truthy/falsy values):

```js
let username = "";

if (username) {
  console.log(`Welcome, ${username}`);
} else {
  console.log("No username provided");
}
```

Since `""` is falsy, this prints "No username provided". Be careful with numbers: `if (count)` will skip the block when `count` is `0`, which is sometimes unintended.

---

## 8.3 The switch Statement

`switch` compares one value against several possible matches — a cleaner alternative to a long `if / else if` chain when checking the same variable repeatedly:

```js
let day = "Tuesday";

switch (day) {
  case "Monday":
    console.log("Start of the week");
    break;
  case "Tuesday":
  case "Wednesday":
    console.log("Midweek");
    break;
  case "Friday":
    console.log("Almost the weekend");
    break;
  default:
    console.log("Some other day");
}
```

- `switch` uses strict (`===`) comparison.
- `break` stops execution from "falling through" into the next case — omitting it (as with `"Tuesday"` above, which shares logic with `"Wednesday"`) is sometimes intentional but usually a bug if accidental.
- `default` runs when no case matches — it's optional but good practice to include.

---

## 8.4 Nesting And Combining Conditions

Conditions can be nested, and logical operators (Lesson 7) let you combine multiple checks in one line:

```js
let age = 25;
let hasTicket = true;

if (age >= 18) {
  if (hasTicket) {
    console.log("Entry allowed");
  } else {
    console.log("Need a ticket");
  }
} else {
  console.log("Must be 18+");
}

// Often better expressed flatly:
if (age >= 18 && hasTicket) {
  console.log("Entry allowed");
}
```

Prefer flattening conditions with `&&` / `||` over deep nesting when possible — it keeps code easier to read and debug.

---

## 8.5 The Ternary Operator As A Compact Conditional

For a simple either/or decision that produces a value, the ternary operator (introduced in Lesson 7) is often cleaner than a full `if/else`:

```js
function getFee(age) {
  return age < 12 ? "Free" : "$10";
}

console.log(getFee(8));  // "Free"
console.log(getFee(30)); // "$10"
```

Avoid chaining multiple ternaries together (`a ? b : c ? d : e`) — it quickly becomes hard to read. Use `if / else if` for anything beyond one simple choice.

[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[9]-Loops.md)
