[Previous](./[0]-Introduction-to-Angular.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[2]-TypeScript-For-Angular.md)

*Basics*

# Lesson 1 - What Is Angular

Before writing any code, it helps to know what Angular *is*, where it came from, and how to set up a working project. This lesson gets you from zero to a running app and explains what every generated file is for.

---

## 1.1 Angular as an opinionated platform

Angular is a **framework** (some people say *platform*) for building single-page applications (SPAs). A SPA loads one HTML page and then updates it with JavaScript as the user navigates, instead of requesting a new page from the server each time.

"Opinionated" means Angular makes many decisions for you. Where a library like React leaves you to pick a router, a form solution, and an HTTP tool, Angular ships official answers to all of them:

| Need | Built-in Angular answer |
|---|---|
| Building UI | Components and templates |
| Navigation between pages | `@angular/router` |
| Forms and validation | `@angular/forms` |
| Talking to servers | `HttpClient` (`@angular/common/http`) |
| Sharing logic and state | Services and dependency injection |
| Reactivity | Signals (and RxJS for event streams) |
| Tooling | Angular CLI (`ng`) |
| Testing | `TestBed` and the CLI test runner |

The trade-off is a steeper learning curve at the start in exchange for consistency later. Two developers who have never met usually find the same things in the same places in an Angular project.

Angular is written in **TypeScript** (a typed superset of JavaScript) and maintained by Google. A new major version is released roughly every six months.

---

## 1.2 Angular vs AngularJS

These are two different frameworks that share a name:

| | AngularJS | Angular |
|---|---|---|
| Also called | Angular 1.x | Angular 2+ (just "Angular") |
| Language | JavaScript | TypeScript |
| Building blocks | Controllers, `$scope`, directives | Components, services, signals |
| Released | 2010 | Rewritten from scratch in 2016 |
| Status | End of life (long-term support ended December 2021) | Actively developed |

The jump from AngularJS to Angular was a full rewrite, not an upgrade. When you search online, check that a tutorial uses `@Component` and TypeScript. If it mentions `$scope` or `ng-controller`, it is about AngularJS and does not apply here.

Even within modern Angular, the style has changed. Older tutorials use `NgModule`, `*ngIf`, and `*ngFor`. Newer code uses standalone components, `@if` / `@for`, and signals. This course teaches the modern style and points out the older style where you will still meet it in real codebases.

---

## 1.3 Installing the Angular CLI

The **Angular CLI** is a command-line tool that creates, runs, builds, and tests Angular projects.

**Step 1 - Install Node.js.** Angular tooling runs on Node.js. Install a currently supported LTS release from [nodejs.org](https://nodejs.org). Each Angular version supports specific Node versions, listed in the [version compatibility table](https://angular.dev/reference/versions).

```bash
node --version
npm --version
```

**Step 2 - Install the CLI globally.**

```bash
npm install -g @angular/cli
ng version
```

**Step 3 - Create and run a project.**

```bash
ng new my-app
cd my-app
ng serve
```

`ng new` asks a few questions (stylesheet format, whether to enable server-side rendering, and so on). The defaults are fine while learning. `ng serve` starts a development server, usually at `http://localhost:4200`, and reloads the browser whenever you save a file.

The commands you will use most:

| Command | What it does |
|---|---|
| `ng new <name>` | Create a new project |
| `ng serve` | Run the dev server with live reload |
| `ng generate <type> <name>` (or `ng g`) | Create a component, service, pipe, and so on |
| `ng build` | Produce production files |
| `ng test` | Run unit tests |
| `ng update` | Upgrade Angular and its packages |
| `ng add <package>` | Install a package and run its setup |

Example: `ng generate component user-card` creates a new component folder with all the files it needs.

---

## 1.4 Tour of a generated project

A fresh project looks roughly like this (exact names vary by version):

```
my-app/
├── public/                  # Static files copied as-is (favicon, images)
├── src/
│   ├── app/
│   │   ├── app.ts           # Root component (class + decorator)
│   │   ├── app.html         # Root component template
│   │   ├── app.css          # Root component styles
│   │   ├── app.config.ts    # Application-wide providers
│   │   └── app.routes.ts    # Route definitions
│   ├── index.html           # The single HTML page
│   ├── main.ts              # Entry point that starts the app
│   └── styles.css           # Global styles
├── angular.json             # CLI build/serve/test configuration
├── package.json             # Dependencies and npm scripts
└── tsconfig.json            # TypeScript compiler settings
```

> **Version note:** Angular 20 and newer generate short names like `app.ts` with a class called `App`. Earlier versions generate `app.component.ts` with a class called `AppComponent`. Both work the same way, and you will see both in the wild. The lessons in this topic use the longer `...Component` / `.component.ts` names because they are common in existing codebases and make the role of each file obvious.

What the key files do:

- **`index.html`** contains a custom element such as `<app-root></app-root>`. This is the placeholder where Angular renders your app.
- **`main.ts`** starts the application:

  ```ts
  import { bootstrapApplication } from '@angular/platform-browser';
  import { appConfig } from './app/app.config';
  import { AppComponent } from './app/app.component';

  bootstrapApplication(AppComponent, appConfig).catch((err) => console.error(err));
  ```

- **`app.config.ts`** lists application-wide features (router, HTTP client, and so on) as *providers*:

  ```ts
  import { ApplicationConfig } from '@angular/core';
  import { provideRouter } from '@angular/router';
  import { routes } from './app.routes';

  export const appConfig: ApplicationConfig = {
    providers: [provideRouter(routes)],
  };
  ```

- **The root component** (`AppComponent`) is the top of your component tree. Every other component lives somewhere beneath it.
- **`angular.json`** tells the CLI how to build, serve, and test. You rarely edit it by hand early on.

Open the project in an editor, run `ng serve`, change some text in the root template, and watch the browser update. That loop is how you will learn the rest of this topic.

---

**Further reading:** [Angular overview](https://angular.dev/overview) · [Installation](https://angular.dev/installation)

[Previous](./[0]-Introduction-to-Angular.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[2]-TypeScript-For-Angular.md)
