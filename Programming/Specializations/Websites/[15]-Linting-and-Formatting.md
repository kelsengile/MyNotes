[Previous](./[14]-Version-Control-with-Git.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[16]-Testing-JavaScript.md)

*Tooling & Build Systems*

# Lesson 15 - Linting & Code Formatting (ESLint, Prettier)

## 15.1 Linting vs Formatting

These solve two related but different problems. A **linter** analyzes code for potential bugs and style violations (an unused variable, a missing `break` in a `switch`, an accidental `==` instead of `===`). A **formatter** rewrites code's whitespace, line breaks, and punctuation into a consistent style, without touching its logic. Using both means code is both correct-by-convention and visually uniform, regardless of who wrote it.

---

## 15.2 ESLint

**ESLint** is the standard JavaScript/TypeScript linter. It's configured with rules, often extending a shared preset:

```js
// eslint.config.js
export default [
  {
    rules: {
      "no-unused-vars": "warn",
      "eqeqeq": "error"
    }
  }
];
```

Editors like VS Code surface ESLint warnings inline as you type, and `npm run lint` typically runs it across the whole project — often as a required check before code can be merged.

---

## 15.3 Prettier

**Prettier** is an opinionated code formatter — it makes almost no configuration decisions on purpose, so teams stop debating tabs-vs-spaces or quote styles. Running it rewrites code in place:

```bash
npx prettier --write .
```

Most editors can be configured to run Prettier automatically on save.

---

## 15.4 Using Them Together

ESLint and Prettier can conflict if both try to enforce formatting rules. The common setup disables ESLint's own formatting rules and lets Prettier own formatting entirely, while ESLint focuses purely on catching bugs and enforcing code-quality rules — each tool doing the job it's best at.

---

## 15.5 Enforcing Standards Automatically

Beyond editor integration, teams often run linting and formatting checks in **pre-commit hooks** (via tools like Husky) or as a required step in CI pipelines (Lesson 30), so inconsistently styled or lint-failing code can't be merged at all, regardless of whether an individual contributor's editor is configured correctly.

---

[Previous](./[14]-Version-Control-with-Git.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[16]-Testing-JavaScript.md)
