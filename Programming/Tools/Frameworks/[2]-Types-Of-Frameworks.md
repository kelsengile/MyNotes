[Previous](./[1]-What-Are-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[3]-The-Framework-Ecosystem.md)

*Basics*

# Lesson 2 - Types Of Frameworks

Not all frameworks solve the same problem. Some run in the browser and draw interfaces, some run on servers and handle data, and some produce mobile apps. This lesson sorts frameworks into families so that when you meet a new tool you can immediately tell what job it is meant for.

Here is the big picture before we look at each family in detail:

| Family | Runs where? | Main job | Example tools |
|---|---|---|---|
| Frontend | In the user's browser | Build interactive user interfaces | React, Angular, Vue.js, Svelte |
| Backend | On a server | Handle requests, data, and business logic | Express, NestJS, Django, Flask, FastAPI, Spring Boot, Laravel, Ruby on Rails, ASP.NET Core |
| Full-stack / meta-framework | Browser and server | Combine UI, routing, and server code in one project | Next.js |
| Mobile / cross-platform | On phones and tablets | Build iOS and Android apps from one codebase | Flutter, React Native |

A typical web application is made of a frontend and a backend that talk to each other over the network:

```text
  Browser / Phone                         Server
┌─────────────────┐   HTTP requests   ┌──────────────────┐      ┌──────────┐
│ Frontend (UI)   │ ────────────────► │ Backend (logic)  │ ───► │ Database │
│ React, Vue ...  │ ◄──────────────── │ Express, Django  │ ◄─── │          │
└─────────────────┘   JSON / HTML     └──────────────────┘      └──────────┘
```

## 2.1 Frontend Frameworks

A **frontend framework** (or library) runs in the user's browser and is responsible for what people *see and interact with*: buttons, forms, lists, menus, and animations.

Frontend frameworks typically provide:

- **Components**: reusable building blocks of the interface (Lesson 8).
- **State management**: a way to track data that changes while the app is running (Lesson 9).
- **Routing**: switching between "pages" without reloading the browser (Lesson 10).
- **A rendering system** that updates the screen efficiently when data changes.

Before such tools existed, developers manually found elements on the page and changed them one by one. Frontend frameworks let you describe *what the interface should look like for a given set of data* and handle the updating for you.

Most frontend tools are used to build **single-page applications (SPAs)**: apps that load once and then update the page dynamically, without a full page reload for every click.

| Tool | Notes |
|---|---|
| **React** | A UI *library* built around components; very large ecosystem |
| **Angular** | A full-featured TypeScript framework with routing, forms, and dependency injection built in |
| **Vue.js** | A "progressive" framework with a gentle learning curve that can scale from small widgets to large apps |
| **Svelte** | Compiles components into small, efficient JavaScript at build time |

> **Styling frameworks:** A *CSS framework* such as **Tailwind CSS** is also a frontend tool, but it focuses on the *look and layout* of the interface rather than its behavior. It is usually used together with one of the tools above. See Lesson 11.

---

## 2.2 Backend Frameworks

A **backend framework** runs on a server, out of sight of the user. It receives requests from frontends or mobile apps, applies rules, reads and writes data, and sends back responses.

Backend frameworks typically provide:

- **Routing** of incoming requests to the right piece of code (Lesson 10, Lesson 12).
- **Middleware** for tasks that apply to many requests, such as logging and authentication (Lesson 13).
- **Database tools** such as ORMs and migrations (Lesson 14).
- **Security features** such as password handling and protection against common attacks (Lesson 15).

Backend frameworks come in two broad styles:

- **Full-featured (batteries-included):** a lot is built in, including database tools, an authentication system, and often an admin interface. They suit larger applications and teams that want a clear way to do things.
- **Minimal (micro-frameworks):** they handle routing and requests and leave the rest to add-on packages. They suit small services and developers who want to pick their own tools.

| Language | Full-featured | Minimal / focused |
|---|---|---|
| JavaScript / TypeScript | NestJS | Express |
| Python | Django | Flask, FastAPI |
| Java | Spring Boot | (many lightweight options) |
| PHP | Laravel | (many lightweight options) |
| Ruby | Ruby on Rails | (many lightweight options) |
| C# | ASP.NET Core | ASP.NET Core minimal APIs |

Some backend frameworks, such as **FastAPI**, are focused on building **APIs**: servers that return data (usually JSON) for other programs to consume. Others, such as **Django**, **Laravel**, and **Rails**, are also very good at generating complete HTML pages on the server. Both approaches are common.

---

## 2.3 Full-Stack and Meta-Frameworks

The term **full-stack** is used in two different ways, so it helps to know both.

**1. Full-stack framework (traditional meaning).** A backend framework that also covers the user-facing part by rendering HTML pages on the server. Django, Laravel, and Ruby on Rails work this way: one project handles routing, database access, logins, and page templates.

**2. Meta-framework (modern JavaScript meaning).** A framework built *on top of* a frontend library or framework, adding the pieces the library does not provide on its own. **Next.js** is the best-known example. It builds on React and adds:

- **File-based routing**: the folder structure defines the URLs.
- **Server-side rendering and static generation**: pages can be built ahead of time or on the server, which helps with load speed and search engines (Lesson 18).
- **API routes**: backend endpoints live in the same project as the frontend.

Other meta-frameworks follow the same idea for other tools. For example, Nuxt builds on Vue and SvelteKit builds on Svelte.

```text
React (UI library)
   └── Next.js (adds routing, server rendering, API routes, build tooling)
```

Meta-frameworks reduce the number of decisions you need to make and let a small team build a complete product inside one project. The trade-off is that you are tied to the meta-framework's way of doing things, on top of the underlying library.

---

## 2.4 Mobile and Cross-Platform Frameworks

Phones and tablets run on two major platforms, **iOS** and **Android**, and each has its own preferred languages and tools. Building a separate app for each means writing and maintaining two codebases.

There are two broad approaches:

| Approach | Description | Trade-off |
|---|---|---|
| **Native development** | Separate apps built with each platform's own tools | Best access to platform features, but double the work |
| **Cross-platform development** | One codebase that produces apps for several platforms | Less duplicated effort, but you depend on the framework's support for platform features |

The two most widely used cross-platform tools in this Topic are:

- **Flutter** uses the **Dart** language. It draws its own interface using its own rendering engine and a large set of built-in widgets, so apps look and behave consistently across platforms. It can also target web and desktop from the same codebase.
- **React Native** uses **JavaScript** (or TypeScript) and React concepts. It maps your components to the platform's real native interface elements, so JavaScript and React developers can reuse their skills on mobile.

Mobile frameworks share many concepts with web frameworks, including components, state, and routing, but navigation works differently (Lesson 10), and apps are delivered through app stores rather than web servers (Lesson 17).

---

[Previous](./[1]-What-Are-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[3]-The-Framework-Ecosystem.md)
