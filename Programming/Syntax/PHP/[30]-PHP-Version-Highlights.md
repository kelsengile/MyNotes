[Previous](./[29]-Reflection.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[31]-Working-with-Files.md)

*Advanced Language Features*

# Lesson 30 - PHP Version Highlights

## 30.1 PHP 7.x Highlights

PHP 7 (2015) was a major performance and language overhaul:
- Roughly doubled performance over PHP 5.
- Introduced scalar type declarations (`int`, `string`, `float`, `bool`) and return types.
- Added the null coalescing operator (`??`) and the spaceship operator (`<=>`), both covered in Lesson 7.
- PHP 7.4 added typed properties and arrow functions (`fn`), covered in Lesson 10.

---

## 30.2 PHP 8.0 Highlights

PHP 8.0 (2020) brought some of the most significant additions covered throughout this course:
- The **match** expression (Lesson 8).
- **Named arguments**, letting you pass function arguments by name instead of position: `greet(name: "Sam")`.
- **Constructor property promotion** (Lesson 16).
- **Union types** (Lesson 27) and **attributes** (Lesson 28).
- The **nullsafe operator** `?->`, which stops silently instead of erroring when chaining calls on a possibly-null value: `$user?->address?->city`.

---

## 30.3 PHP 8.1 Highlights

PHP 8.1 (2021) added:
- **Enums**, both pure and backed (Lesson 23).
- **readonly properties** (Lesson 18).
- **Fibers**, a low-level building block for concurrency used internally by some async frameworks.
- **Intersection types**, requiring a value to satisfy multiple types at once (`Countable&Iterator`).

---

## 30.4 PHP 8.2 – 8.4 Highlights

- **8.2** — Readonly classes (every property is readonly automatically), and standalone types like `null` and `false` as full return/parameter types.
- **8.3** — Typed class constants, and the `#[Override]` attribute mentioned in Lesson 28.
- **8.4** — Property hooks (custom logic on property get/set without full getter/setter boilerplate), and the `array_find`/`array_any`/`array_all` helper functions.

Keeping track of version highlights helps you recognize modern PHP code, and understand which features are safe to use depending on the minimum PHP version a project supports.

---

[Previous](./[29]-Reflection.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[31]-Working-with-Files.md)
