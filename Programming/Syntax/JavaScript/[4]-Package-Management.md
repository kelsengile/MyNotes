[Previous](./[3]-How-JavaScript-Works.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B5%5D-Variables-and-Data-Types%20%281%29.md)

*Getting Started*

# Lesson 4 - Package Management

## 4.1 What Is A Package?

A **package** is a reusable piece of code someone else has written and published — anything from a small helper function to an entire framework like React. Rather than writing everything from scratch, JavaScript developers rely heavily on packages published to the **npm registry**, a public catalog of hundreds of thousands of packages.

---

## 4.2 Initializing A Project With package.json

Every Node.js project starts with a `package.json` file, which describes the project and tracks its dependencies. Create one by running, inside your project folder:

```bash
npm init -y
```

The `-y` flag accepts all the defaults. This generates a `package.json` similar to:

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  }
}
```

---

## 4.3 Installing Packages

To install a package and add it to your project:

```bash
npm install lodash
```

This does two things: it downloads the package into a `node_modules` folder, and it adds `lodash` to the `dependencies` field of `package.json`. To install a package only for development use (like a testing tool), add `--save-dev`:

```bash
npm install --save-dev jest
```

To remove a package:

```bash
npm uninstall lodash
```

---

## 4.4 Using An Installed Package

Once installed, you can bring a package into your code:

```js
const _ = require("lodash");

console.log(_.capitalize("hello world")); // "Hello world"
```

Lesson 29 covers `require()` and the newer `import` syntax in detail — for now, just know that installed packages become available to your code this way.

---

## 4.5 package-lock.json And Reproducible Installs

Alongside `package.json`, npm creates a `package-lock.json` file. It records the *exact* version of every package (and every package those packages depend on) that was installed. Committing this file to version control ensures anyone else who runs `npm install` gets identical versions — avoiding "it works on my machine" bugs caused by mismatched package versions.

---

## 4.6 Alternative Package Managers: Yarn And pnpm

npm ships with Node.js by default, but two popular alternatives solve similar problems differently:

- **Yarn** — historically faster than early npm versions, with a similar command style (`yarn add lodash` instead of `npm install lodash`).
- **pnpm** — stores packages in a single shared location on disk and links to them, saving significant disk space across projects (`pnpm add lodash`).

All three read and understand `package.json`, so switching between them on an existing project is usually painless. This course uses npm in its examples, but the concepts transfer directly.

[Previous](./[3]-How-JavaScript-Works.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B5%5D-Variables-and-Data-Types%20%281%29.md)
