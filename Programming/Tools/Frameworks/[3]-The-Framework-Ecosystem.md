[Previous](./[2]-Types-Of-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[4]-Project-Structure-And-Conventions.md)

*Basics*

# Lesson 3 - The Framework Ecosystem

A framework never exists alone. It is written in a language, executed by a runtime, and assembled from packages fetched by a package manager. In this lesson you will see how those pieces fit together, learn the single most important idea that separates frameworks from libraries (Inversion of Control), and get a quick tour of popular frameworks.

## 3.1 Languages and Their Frameworks

Every framework is written for a specific programming language, so your choice of language largely narrows your choice of frameworks. The table below shows the common pairings used in this Topic.

| Language | Common frameworks | Typically used for |
|---|---|---|
| **JavaScript / TypeScript** | React, Angular, Vue.js, Svelte, Next.js, Express, NestJS, React Native | Web frontends, APIs, and mobile apps |
| **Python** | Django, Flask, FastAPI | Web backends, APIs, data-driven apps |
| **Java** | Spring Boot | Enterprise backends and microservices |
| **PHP** | Laravel | Web applications and websites |
| **Ruby** | Ruby on Rails | Web applications |
| **C#** | ASP.NET Core | Web apps, APIs, cloud services |
| **Dart** | Flutter | Mobile, web, and desktop apps |
| **CSS** (styling language) | Tailwind CSS | Styling user interfaces |

A few things to notice:

- **JavaScript is unusual because it runs everywhere.** It is the only language that runs natively in every web browser, and with Node.js it also runs on servers. This is why one language can power an entire application, from frontend to backend to mobile.
- **TypeScript** is JavaScript with added type checking. Browsers cannot run it directly, so it is converted ("compiled") to JavaScript first. Angular and NestJS are built around TypeScript, and most other JavaScript frameworks support it.
- **Language choice is often a team decision.** If your team already knows Python, Django, Flask, or FastAPI are natural choices. If they know Java, Spring Boot is. Skills you already have are a real advantage.
- **Concepts transfer between languages.** Routing, components, state, and middleware exist in almost every framework. Learning them once helps you in all of them.

---

## 3.2 Runtimes and Package Managers

### Runtimes

A **runtime** is the environment that executes your code (see Lesson 1). Each language ecosystem has its own:

| Language | Runtime / execution environment |
|---|---|
| JavaScript (browser) | The browser's built-in JavaScript engine |
| JavaScript (server) | Node.js |
| Python | The Python interpreter |
| Java | The Java Virtual Machine (JVM) |
| C# | The .NET runtime |
| PHP | The PHP interpreter, usually behind a web server |
| Dart | The Dart VM during development, or compiled native code in release builds |

When you install a framework, you must have a compatible runtime version installed. Frameworks state which runtime versions they support, and using an unsupported version is a very common cause of confusing errors.

Because different projects often need different runtime versions, developers commonly use **version managers** to install and switch between them (for example, nvm for Node.js or pyenv for Python).

### Package managers

A **package manager** downloads and manages the third-party code your project depends on, including the framework itself. Each ecosystem has its own, and each downloads from a public collection called a **registry**:

| Ecosystem | Package manager | Registry |
|---|---|---|
| JavaScript / TypeScript | npm (also yarn, pnpm) | npm registry |
| Python | pip | PyPI |
| Java | Maven or Gradle | Maven Central |
| PHP | Composer | Packagist |
| C# | NuGet | nuget.org |
| Ruby | Bundler / RubyGems | RubyGems |
| Dart / Flutter | pub | pub.dev |

You will learn how package managers work, how versioning works, and how to keep dependencies safe in Lesson 5.

---

## 3.3 Inversion of Control ("The Framework Calls You")

**Inversion of Control (IoC)** is the key idea that makes a framework a framework and not just a library.

- With a **library**, *your* code is in charge. You write the main flow of the program and call library functions when you need them.
- With a **framework**, the *framework* is in charge. It runs the main flow and calls *your* code at specific moments, such as "a request for this URL has arrived" or "the user clicked this button."

A common way to remember this is the saying "don't call us, we'll call you."

The control has been *inverted*: instead of your code calling the tool, the tool calls your code.

### A tiny framework in action

The following mini "framework" shows the idea in a few lines. You register handlers, and the framework decides when to run them:

```javascript
class MiniApp {
  constructor() {
    this.routes = {};
  }

  // You register your code...
  get(path, handler) {
    this.routes[path] = handler;
  }

  // ...and the "framework" decides when to run it.
  handle(path) {
    const handler = this.routes[path];
    return handler ? handler() : "404 Not Found";
  }
}

const app = new MiniApp();
app.get("/", () => "Home page");
app.get("/about", () => "About page");

console.log(app.handle("/about"));
console.log(app.handle("/missing"));
```

Output:

```text
About page
404 Not Found
```

Notice that your handler functions (`() => "Home page"`) never call themselves. The `MiniApp` class owns the flow and calls the right handler. Real frameworks do exactly this, on a much larger scale.

### Where you see IoC in real frameworks

| Form of IoC | Description | Example |
|---|---|---|
| **Route handlers** | You register a function for a URL; the framework calls it on a matching request | `app.get("/hello", handler)` |
| **Lifecycle hooks** | You define functions that run at set moments in a component's life | "when the component appears," "when it is removed" |
| **Event handlers** | You attach code to events; the framework calls it when the event occurs | A button's click handler |
| **Dependency injection** | You declare what you need; the framework creates it and passes it in | Services in Angular, NestJS, and Spring Boot |

### Dependency injection

**Dependency injection (DI)** is a particular form of IoC. Instead of a class creating the objects it depends on, those objects are *handed to it from outside*:

```javascript
class EmailService {
  send(to, message) {
    console.log(`Email to ${to}: ${message}`);
  }
}

class SignupService {
  // The dependency is handed in from outside instead of created here.
  constructor(mailer) {
    this.mailer = mailer;
  }

  register(email) {
    this.mailer.send(email, "Welcome!");
  }
}

const service = new SignupService(new EmailService());
service.register("ada@example.com");
```

Output:

```text
Email to ada@example.com: Welcome!
```

Here `SignupService` does not know how email is actually sent. It only knows that it receives something with a `send` method. This makes the code easier to change and easier to test, because a fake mailer can be passed in during tests. Frameworks such as Angular, NestJS, and Spring Boot automate this: you declare what a class needs, and the framework builds and supplies it.

### Why IoC matters to you as a learner

Understanding IoC changes how you read framework documentation. Instead of asking "how do I call this?", you ask **"where does the framework expect me to put my code, and when will it run it?"** Most framework tutorials are really answering that second question.

---

## 3.4 Popular Framework Examples

The table below gives a quick reference for the tools covered in this course. Each one is explored in its own tool-specific folder after this Topic.

| Tool | Language | Category | Best known for |
|---|---|---|---|
| **React** | JavaScript / TypeScript | Frontend UI library | Component-based UIs and a huge ecosystem |
| **Angular** | TypeScript | Frontend framework | Complete, structured framework for large apps |
| **Vue.js** | JavaScript / TypeScript | Frontend framework | Gentle learning curve and flexibility |
| **Svelte** | JavaScript / TypeScript | Frontend framework / compiler | Compiling away the framework for small, fast output |
| **Next.js** | JavaScript / TypeScript | Full-stack meta-framework (on React) | Server rendering, file-based routing, API routes |
| **Node.js** | JavaScript | Runtime | Running JavaScript on servers |
| **Express** | JavaScript | Minimal backend framework | Simple, flexible web servers and APIs |
| **NestJS** | TypeScript | Structured backend framework | Modular, Angular-inspired server applications |
| **Django** | Python | Full-featured backend framework | Batteries included: ORM, admin, authentication |
| **Flask** | Python | Micro backend framework | Small and flexible; add only what you need |
| **FastAPI** | Python | API framework | Type-hint-based validation and automatic documentation |
| **Spring Boot** | Java | Backend framework | Production-ready enterprise applications |
| **Laravel** | PHP | Full-featured backend framework | Elegant syntax and rich built-in tooling |
| **Ruby on Rails** | Ruby | Full-featured backend framework | Popularized convention over configuration |
| **ASP.NET Core** | C# | Backend framework | High-performance, cross-platform web apps and APIs |
| **Tailwind CSS** | CSS | Styling framework | Utility-first styling directly in markup |
| **Flutter** | Dart | Cross-platform UI framework | One codebase for mobile, web, and desktop |
| **React Native** | JavaScript / TypeScript | Cross-platform mobile framework | Native mobile apps using React skills |

Popularity changes over time. New tools appear, and old ones fade or reinvent themselves. The concepts in the rest of this Topic stay valuable no matter which names are fashionable.

---

[Previous](./[2]-Types-Of-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[4]-Project-Structure-And-Conventions.md)
