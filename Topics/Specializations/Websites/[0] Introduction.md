# Introduction to Web Development

Welcome! This is the starting point for a structured path into web development — from absolute basics to building and deploying real projects. Each numbered lesson in the Table of Contents corresponds to its own markdown file (e.g. `01-html-basics.md`) so you can track progress and revisit topics easily.

---

## How to Use This Course

- Work through lessons **in order** — later lessons assume knowledge from earlier ones.
- Each lesson should include: a short explanation, code examples, and a small hands-on exercise or mini-project.
- Don't rush HTML/CSS — a shaky foundation makes JavaScript and frameworks harder later.
- Build something after every 3–4 lessons, even something tiny. Projects cement concepts far better than reading alone.
- Keep a `notes/` folder or personal `.md` files for anything that trips you up — you'll thank yourself later.

---

## Prerequisites

- A computer (Windows, macOS, or Linux)
- A code editor (recommended: [VS Code](https://code.visualstudio.com/))
- A modern browser (Chrome, Firefox, or Edge) with developer tools
- Basic comfort using files/folders on your computer
- No prior programming experience required

---

## Tools You'll Set Up Along the Way

| Tool | Purpose | Introduced In |
|---|---|---|
| VS Code | Code editor | Lesson 1 |
| Browser DevTools | Inspect & debug pages | Lesson 1 |
| Git & GitHub | Version control | Lesson 8 |
| Node.js & npm | JavaScript runtime & package manager | Lesson 12 |
| A frontend framework (React) | Building dynamic UIs | Lesson 16 |
| A terminal / command line | Running commands | Lesson 1 |

---

## Table of Contents

### Part 1 — Foundations
1. [Setting Up Your Environment](./01-environment-setup.md)
2. [How the Web Works (HTTP, DNS, Browsers, Servers)](./02-how-the-web-works.md)
3. [HTML Basics — Structure & Semantics](./03-html-basics.md)
4. [CSS Basics — Selectors, Box Model, Layout](./04-css-basics.md)
5. [CSS Layout — Flexbox & Grid](./05-css-layout.md)
6. [Responsive Design & Media Queries](./06-responsive-design.md)
7. **Project 1: Build a Personal Landing Page**

### Part 2 — Version Control & Tooling
8. [Git & GitHub Basics](./08-git-github.md)
9. [Browser DevTools Deep Dive](./09-devtools.md)
10. [Web Accessibility (a11y) Basics](./10-accessibility.md)

### Part 3 — JavaScript
11. [JavaScript Fundamentals (Variables, Types, Functions)](./11-js-fundamentals.md)
12. [JavaScript Control Flow & Arrays/Objects](./12-js-control-flow.md)
13. [DOM Manipulation & Events](./13-dom-events.md)
14. [Asynchronous JavaScript (Promises, Fetch, Async/Await)](./14-async-js.md)
15. [Working with APIs](./15-apis.md)
16. **Project 2: Build an Interactive To-Do App**

### Part 4 — Modern Frontend Development
17. [Node.js & npm Basics](./17-node-npm.md)
18. [Introduction to React](./18-react-intro.md)
19. [React Components, Props & State](./19-react-components.md)
20. [React Hooks & Side Effects](./20-react-hooks.md)
21. [Routing in Single Page Apps](./21-routing.md)
22. **Project 3: Build a Multi-Page React App**

### Part 5 — Backend & Full Stack
23. [Introduction to Backend Development (Node/Express)](./23-backend-intro.md)
24. [REST APIs & CRUD Operations](./24-rest-apis.md)
25. [Databases (SQL vs NoSQL Basics)](./25-databases.md)
26. [Authentication & Authorization Basics](./26-auth.md)
27. **Project 4: Full Stack CRUD Application**

### Part 6 — Shipping It
28. [Testing Basics (Unit & Integration)](./28-testing.md)
29. [Deployment (Netlify, Vercel, Render, etc.)](./29-deployment.md)
30. [Performance & Best Practices](./30-performance.md)
31. **Capstone Project: Design, Build & Deploy Your Own App**

---

## Project Development Guidelines

Use these conventions for every project in this course, starting from Project 1. Consistency now builds good habits for real-world work later.

### Recommended File Structure (Frontend Project)

```
project-name/
├── README.md                 # What the project is, how to run it
├── .gitignore                 # Files/folders Git should ignore
├── index.html                  # Entry point (for plain HTML/CSS/JS projects)
├── package.json                 # Project metadata & dependencies (once npm is introduced)
│
├── src/                          # All source code lives here
│   ├── assets/                     # Images, fonts, icons
│   ├── styles/                       # CSS or Sass files
│   │   ├── main.css
│   │   └── components/
│   ├── scripts/                        # JavaScript files
│   │   ├── main.js
│   │   └── utils.js
│   └── components/                       # Reusable UI pieces (React projects)
│
├── public/                        # Static files served as-is
│
├── tests/                          # Test files
│
└── docs/                            # Notes, wireframes, planning docs
```

### Recommended File Structure (Full Stack Project)

```
project-name/
├── README.md
├── .gitignore
├── client/                     # Frontend app
│   └── src/
├── server/                     # Backend app
│   ├── routes/
│   ├── controllers/
│   ├── models/
│   └── index.js
├── .env.example                # Sample environment variables (never commit real .env)
└── package.json
```

### Naming Conventions

| Item | Convention | Example |
|---|---|---|
| Folders | kebab-case | `user-profile/` |
| HTML/CSS files | kebab-case | `main-styles.css` |
| JS variables/functions | camelCase | `getUserData()` |
| React components | PascalCase | `UserProfile.jsx` |
| CSS classes | kebab-case (or BEM) | `.card-title`, `.card__title--active` |
| Constants | UPPER_SNAKE_CASE | `MAX_USERS` |

### Workflow Checklist for Every Project

1. Plan first — sketch the layout or write a short feature list before coding.
2. Initialize a Git repository and commit early, commit often.
3. Follow the file structure above; don't dump everything into one file.
4. Write a `README.md` with: project description, setup steps, and screenshots if possible.
5. Comment your code where logic isn't obvious — but don't over-comment the obvious.
6. Test in the browser frequently, not just at the end.
7. Check responsiveness (mobile, tablet, desktop) before calling it done.
8. Push to GitHub and, once you reach Part 6, deploy it live.

### Git Commit Message Convention

```
type: short description

feat: add navbar component
fix: correct button alignment on mobile
docs: update README with setup instructions
style: format code with prettier
refactor: simplify form validation logic
```

---

## Learning Tips

- Type code out yourself — avoid copy-pasting examples.
- Break things on purpose, then fix them. That's how debugging skill is built.
- Read error messages fully before searching for help; they usually tell you exactly what's wrong.
- Explain concepts back in your own words (or teach them to someone) to test real understanding.
- It's normal to feel lost sometimes — Google/MDN Web Docs are part of every developer's daily workflow, not a sign of weakness.

---

## Next Step

Head to **[Lesson 1: Setting Up Your Environment](./01-environment-setup.md)** to get started.