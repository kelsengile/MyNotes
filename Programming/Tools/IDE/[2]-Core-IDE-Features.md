[Previous](./[1]-What-Is-An-IDE.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[3]-Popular-IDEs.md)

*IDE Foundations*

# Lesson 2 - Core IDE Features

## 2.1 Syntax Highlighting

Syntax highlighting colors different parts of your code based on their role — keywords, variable names, strings, comments, and so on — so the structure of the code is visible at a glance. It doesn't check whether the code is correct; it's purely visual, but it makes typos like an unclosed quote or bracket much easier to spot, since the coloring will suddenly look wrong.

---

## 2.2 Code Completion And IntelliSense

Code completion suggests what you might type next: variable names, function names, or the properties available on an object. The more advanced version of this, often called IntelliSense (a term popularized by Microsoft but used generically now), understands the actual types in your code and can show you a function's expected parameters and return type as you type, cutting down on trips to documentation.

---

## 2.3 Error Detection And Linting

Most IDEs continuously analyze your code in the background and underline problems before you ever run it — a misspelled variable, a missing semicolon, or a type mismatch. This is often powered by a **linter**, a tool that checks code against a set of rules for correctness and style. Errors are usually shown in red, warnings in yellow, directly in the editor and in a dedicated "Problems" panel.

---

## 2.4 Code Navigation

Once a project grows past a handful of files, finding things by scrolling isn't practical. IDEs provide:

- **Go to Definition** — jump straight to where a function or variable is declared.
- **Find All References** — see every place a symbol is used across the project.
- **Symbol search** — jump to any function, class, or variable by typing its name.

These features rely on the IDE actually understanding your code's structure, not just treating it as text — which is what separates an IDE from a plain text editor.

[Previous](./[1]-What-Is-An-IDE.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[3]-Popular-IDEs.md)
