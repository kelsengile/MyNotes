[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)

# npm
 Packages Fundamentals
npm (Node Package Manager) is the default package manager for JavaScript and Node.js. It installs libraries into a project, tracks them in `package.json`, and ships bundled with Node itself.

Download [https://docs.npmjs.com/downloading-and-installing-node-js-and-npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)

---

## What Is npm?

npm pulls packages from the npm registry, the largest package registry in the world. Every Node.js project typically has a `package.json` file listing its dependencies, and a `node_modules/` folder where those dependencies actually get installed.

---

## Core Commands

```bash
npm init                     # Create a new package.json interactively
npm init -y                  # Create one with default values, no prompts
npm install <package>        # Install a package and add it to package.json
npm install <package> --save-dev   # Install a development-only dependency
npm install -g <package>     # Install a package globally
npm uninstall <package>      # Remove a package
npm install                  # Install everything listed in package.json
npm update                   # Update dependencies to newer allowed versions
npm run <script>             # Run a script defined in package.json
```

---

## package.json and package-lock.json

`package.json` declares which packages your project needs and the version ranges allowed (e.g. `^4.18.2`). `package-lock.json` records the exact versions actually installed — commit both so every environment installs identically.

```json
{
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

---

## Local vs. Global Installs

By default, `npm install` installs into the current project's `node_modules/`. Adding `-g` installs a package globally on your system instead — typically reserved for command-line tools you want available everywhere, not project dependencies.

---

## Example Walkthrough

```bash
npm init -y
npm install express
npm install nodemon --save-dev
npm run start
```

Creates a new project, installs Express as a dependency, adds nodemon as a dev tool, then runs the project's start script.

[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)
