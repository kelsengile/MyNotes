[Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[2]-Running-JavaScript-Code.md)

*Getting Started*

# Lesson 1 - Installation And Setup

## 1.1 What Is Node.js And Why You Need It

JavaScript was originally built to run only inside web browsers. **Node.js** is a runtime that lets JavaScript run outside the browser too — on your computer, on a server, anywhere. You'll use Node.js throughout this course to run scripts, install packages, and eventually build backend applications.

Along with Node.js comes **npm** (Node Package Manager), a tool for installing reusable code written by other developers.

---

## 1.2 Installing Node.js

The easiest way to install Node.js is to download it from the official site:

1. Go to [nodejs.org](https://nodejs.org).
2. Download the **LTS** (Long-Term Support) version — it's the most stable and best for learning.
3. Run the installer and accept the default options.

**Windows / macOS:** the downloaded installer walks you through everything automatically.

**Linux (Debian/Ubuntu):**
```bash
sudo apt update
sudo apt install nodejs npm
```

**Linux/macOS (recommended alternative — a version manager):**
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install --lts
```
A version manager like `nvm` lets you switch between Node.js versions per project, which becomes useful once you work on multiple codebases.

---

## 1.3 Verifying Your Installation

Open a terminal (Command Prompt, PowerShell, or Terminal) and run:

```bash
node -v
npm -v
```

Each command should print a version number, such as `v20.11.0` and `10.2.4`. If you see "command not found," restart your terminal, or your terminal application, and try again — installers sometimes require a fresh session to update your system's PATH.

---

## 1.4 Choosing A Code Editor

You can write JavaScript in any text editor, but a code editor makes life easier with syntax highlighting, autocomplete, and error checking. **Visual Studio Code** (VS Code) is free, popular, and works well for JavaScript out of the box. Download it from [code.visualstudio.com](https://code.visualstudio.com).

Once installed, create a folder for your course work — this is where you'll save every script you write in this series.

---

## 1.5 Your First Script File

Create a file named `hello.js` and add:

```js
console.log("Hello, JavaScript!");
```

`console.log()` prints a value to the terminal — you'll use it constantly, especially while learning and debugging. In the next lesson, you'll learn several ways to actually run this file.

[Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[2]-Running-JavaScript-Code.md)
