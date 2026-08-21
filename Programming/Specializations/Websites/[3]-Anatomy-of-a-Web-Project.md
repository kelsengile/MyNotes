[Previous](./[2]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[4]-Asynchronous-JavaScript.md)

*Getting Started*

# Lesson 3 - Anatomy of a Web Project

## 3.1 A Minimal Project Structure

A simple static project typically looks like this:

```
my-project/
├── index.html      # entry point
├── style.css       # styles
├── script.js       # behavior
└── assets/         # images, fonts, icons
```

`index.html` is the **entry point** — the file a server looks for by default when a folder is requested (e.g. visiting `/` loads `index.html`). Everything else is linked from there via `<link>`, `<script>`, and `<img>` tags.

---

## 3.2 Growing Beyond a Single Page

As projects grow, folders get organized by responsibility rather than file type in many modern setups:

```
src/
├── components/     # reusable UI pieces
├── pages/          # route-level views
├── styles/         # CSS/SCSS files
├── utils/          # helper functions
└── main.js         # application entry point
public/             # static files copied as-is (favicon, robots.txt)
```

This `src` vs `public` split is common in framework-based projects (Lesson 17+): `src` is processed by a build tool, while `public` files are copied untouched.

---

## 3.3 Configuration Files

Real projects accumulate small config files at the root, each telling a specific tool how to behave:

- `package.json` — project metadata and dependencies (Lesson 12)
- `.gitignore` — files Git should not track (Lesson 14)
- `.eslintrc` / `.prettierrc` — linting and formatting rules (Lesson 15)
- `vite.config.js` or similar — bundler configuration (Lesson 13)
- `.env` — environment variables (Lesson 27)

You don't need to understand every one of these yet — this lesson just gives names to files you'll see repeatedly.

---

## 3.4 The Entry Point and the Build Output

In a build-tooled project, there's a difference between **source files** (what you write, e.g. `src/main.js`) and **build output** (what actually ships to the browser, e.g. `dist/main.abc123.js`). A build step compiles, bundles, and optimizes source files into output files. Understanding this split now will make the tooling chapters (Lessons 12–16) click much faster.

---

[Previous](./[2]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[4]-Asynchronous-JavaScript.md)
