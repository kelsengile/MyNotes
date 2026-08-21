[Previous](./[1]-How-The-Web-Works.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[3]-Anatomy-of-a-Web-Project.md)

*Getting Started*

# Lesson 2 - Development Environment

## 2.1 Choosing a Code Editor

Most modern web developers use **Visual Studio Code (VS Code)** — a free, extensible source code editor. It offers built-in support for HTML, CSS, and JavaScript, an integrated terminal, a debugger, and a marketplace of extensions (linters, formatters, Git tooling, language support) that turn it into a full development environment. Other popular choices include WebStorm (a full IDE) and Sublime Text (lightweight), but VS Code's ecosystem and free price point make it the default starting point for most learners.

---

## 2.2 Browser Developer Tools

Every modern browser ships with **DevTools**, accessible via `F12` or right-click → "Inspect." Key panels include:

- **Elements/Inspector** — view and live-edit the DOM and CSS
- **Console** — run JavaScript, view logs and errors
- **Network** — inspect every request/response, timing, and payload
- **Sources/Debugger** — set breakpoints and step through JavaScript
- **Application/Storage** — inspect cookies, local storage, and cached data

Learning to read the Console and Network tabs is one of the highest-leverage skills a beginner can build — most bugs reveal themselves there before you ever need to guess.

---

## 2.3 Running a Local Server

Opening an HTML file directly (`file:///...`) works for the simplest pages, but many browser features (module imports, `fetch` requests, service workers) are blocked or behave differently under the `file://` protocol due to browser security rules. Instead, developers run a **local development server** that serves files over `http://localhost`. VS Code's "Live Server" extension, or running `npx serve` / `python -m http.server` from a terminal, are common lightweight options. Local servers also often support **live reload** — automatically refreshing the browser when a file changes.

---

## 2.4 The Terminal

The command line (Terminal on macOS/Linux, PowerShell or WSL on Windows) is where developers install packages, run build tools, start servers, and use Git. Comfort with basic commands (`cd`, `ls`/`dir`, `mkdir`, running scripts) is assumed for the rest of this course — most tools covered later (npm, Git, bundlers) are terminal-first.

---

## 2.5 Browser Extensions and Testing Tools

Beyond DevTools, useful additions include extensions for inspecting accessibility (e.g. axe DevTools), React/Vue component trees, and API testing tools like Postman or Insomnia for sending requests outside the browser. You don't need all of these on day one, but knowing they exist will save time later in this course.

---

[Previous](./[1]-How-The-Web-Works.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[3]-Anatomy-of-a-Web-Project.md)
