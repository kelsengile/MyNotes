[Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[2]-Compiling-and-Running-TypeScript.md)

*Getting Started*

# Lesson 1 - Installing TypeScript And First-Time Setup

## 1.1 What You Need Before Starting

TypeScript runs on top of Node.js, so you'll need Node.js and its package manager, `npm`, installed first.

- Download Node.js from [nodejs.org](https://nodejs.org) (the LTS version is recommended).
- Verify the install by running:

```bash
node -v
npm -v
```

If both commands print version numbers, you're ready to install TypeScript itself.

---

## 1.2 Installing TypeScript

TypeScript is distributed as an npm package. You can install it **globally** (available anywhere on your machine) or **locally** (scoped to a single project). Local installs are the general recommendation, since they pin an exact compiler version per project.

```bash
# Global install
npm install -g typescript

# Local install (recommended)
npm install --save-dev typescript
```

After a local install, the compiler binary lives inside `node_modules/.bin/typescript`, and you can run it through `npx`:

```bash
npx tsc --version
```

---

## 1.3 Verifying the Compiler

Once installed, confirm TypeScript is available and check its version:

```bash
tsc --version
# or, for a local install
npx tsc --version
```

`tsc` stands for "TypeScript Compiler" — it's the command-line tool that turns `.ts` files into plain `.js` files. You'll use it constantly throughout this course.

---

## 1.4 Setting Up Your First Project Folder

A minimal TypeScript project just needs a folder and a `package.json`:

```bash
mkdir my-ts-project
cd my-ts-project
npm init -y
npm install --save-dev typescript
```

Then create a `tsconfig.json`, which tells the compiler how to behave (covered in depth in Lesson 3):

```bash
npx tsc --init
```

This generates a `tsconfig.json` file with sensible defaults, which you can now start customizing.

---

## 1.5 Writing and Compiling Your First File

Create a file named `index.ts`:

```typescript
function greet(name: string): string {
  return `Hello, ${name}!`;
}

console.log(greet("TypeScript"));
```

Compile it with:

```bash
npx tsc index.ts
```

This produces `index.js`, a plain JavaScript file you can run with `node index.js`. Congratulations — you've just compiled your first TypeScript program.

---

 [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[2]-Compiling-and-Running-TypeScript.md)