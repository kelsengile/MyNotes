 [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[2]-Types-Of-Frameworks.md)

*Basics*

# Lesson 1 - What Are Frameworks?

Almost every modern application is built on top of a framework, yet beginners are often unsure what the word really means. In this lesson you will learn what a framework is, how it differs from the other building blocks developers talk about (libraries and runtimes), why frameworks were invented in the first place, and what you gain and give up when you use one.

## 1.1 Defining a Framework

A **framework** is a reusable, pre-built foundation for an application. It supplies a structure, a set of conventions, and a collection of ready-made tools, and it leaves "gaps" where you plug in the code that is unique to your project.

A helpful analogy is building a house. You *could* start with raw timber and dig your own foundation, but most builders start from a standard plan where the foundation, plumbing, and electrical wiring are already designed. You decide the layout of the rooms, the paint, and the furniture. A framework is that standard plan for software: the repetitive, hard-to-get-right parts are already solved, and you focus on what makes your application different.

Most frameworks bundle some or all of the following:

| Capability | What it does for you |
|---|---|
| **Routing** | Decides which code runs for which URL or screen |
| **Rendering / templating** | Turns your data into HTML (or native UI) |
| **Data access** | Talks to databases in a structured way |
| **Security** | Protects against common attacks and handles logins |
| **Configuration** | Provides a standard place and format for settings |
| **Tooling** | Gives you a command-line tool, a dev server, a test runner, and a build process |
| **Conventions** | Tells you where files go and how things are named |

To see the difference a framework makes, here is a tiny web server written with only the tools built into Node.js. It answers requests to `/hello` and returns "Not found" for everything else. Notice that you must inspect the HTTP method and URL yourself and set the status codes and headers by hand:

```javascript
const http = require("http");

const server = http.createServer((req, res) => {
  if (req.method === "GET" && req.url === "/hello") {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello, world!");
  } else {
    res.writeHead(404, { "Content-Type": "text/plain" });
    res.end("Not found");
  }
});

server.listen(3000, () => console.log("Listening on http://localhost:3000"));
```

Now the same behavior written with a framework (Express):

```javascript
const express = require("express");
const app = express();

app.get("/hello", (req, res) => res.send("Hello, world!"));

app.listen(3000, () => console.log("Listening on http://localhost:3000"));
```

The framework version is shorter because the parts that never change (parsing the request, matching the URL, setting headers, sending a 404 for unknown routes) are handled for you. In a real application with dozens of routes, forms, logins, and a database, that difference becomes enormous.

### Opinionated vs unopinionated frameworks

Frameworks sit on a spectrum:

- **Opinionated ("batteries-included")** frameworks make many decisions for you: folder layout, database layer, login system, and more. Examples include Django, Ruby on Rails, Laravel, Spring Boot, and Angular. You move faster at the start, but you are expected to follow their way of doing things.
- **Minimal ("micro") frameworks** give you the essentials and leave the rest up to you. Examples include Express and Flask. You have more freedom, but you also have to choose and connect extra tools yourself.

Neither is better in general. The right choice depends on your project and your team, which is the subject of the final lesson in this Topic.

> **Note on terminology:** In everyday conversation, "framework" is used loosely. Developers often say "framework" for any large tool they build on, even when it is technically a library or a runtime. The next section explains the differences.

---

## 1.2 Frameworks vs Libraries vs Runtimes

These three words are constantly mixed up, but each describes a different kind of tool.

- A **library** is a collection of reusable code that *you call* when you need it. You stay in control of the application's flow.
- A **framework** is a structure that *calls your code*. It controls the flow and you fill in the pieces it asks for.
- A **runtime** is the environment that actually *executes* your program. It provides the engine that runs the code, plus access to things like files and the network.

### Library: you are in control

When you use a library, your code decides when to call it:

```python
import math

print(math.sqrt(16))  # You decide when to call the library
```

### Framework: the framework is in control

When you use a framework, you write functions and hand them to the framework, which decides when to run them:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/hello")
def hello():
    return "Hello, world!"


# Nothing in this file ever calls hello().
# Flask calls it when a request for /hello arrives.
```

You never write `hello()` anywhere in your own program flow. The framework receives a request, looks up the matching function, and calls it. This reversal of control is so important that it has a name, *Inversion of Control*, and it is explored in detail in Lesson 3.

### Runtime: the place code runs

A runtime is not something you build your app *with* so much as the thing that runs your app. Examples are the JavaScript engine in your browser, **Node.js** (which runs JavaScript outside the browser), the **Java Virtual Machine (JVM)**, the **.NET runtime**, and the **Python interpreter**.

### Comparing the three

| | Library | Framework | Runtime |
|---|---|---|---|
| **Who is in control?** | Your code calls it | It calls your code | It executes everything |
| **Scope** | One focused job | A whole application structure | Running code in general |
| **Typical size of commitment** | Easy to add or remove | Hard to switch later | Tied to the language |
| **Examples** | lodash, Axios, NumPy | Angular, Django, Spring Boot, Express | Node.js, JVM, .NET runtime, Python interpreter |

### How the layers stack up

These tools build on each other:

```text
Your application code
        ▲
   Framework (Express, Django, Angular ...)
        ▲
   Runtime (Node.js, Python interpreter, JVM ...)
        ▲
   Language (JavaScript, Python, Java ...)
```

### Where the lines blur

Real tools do not always fit neatly into one category:

- **React** calls itself a *library* for building user interfaces, yet in practice most projects pair it with other tools to form a complete framework-like setup.
- **Next.js** is a *meta-framework*: a framework built on top of React.
- **Flutter** is a *UI toolkit and framework* that comes with its own rendering engine and widget set.
- **Node.js** is a *runtime*, while **Express** and **NestJS** are frameworks that run on it.

When someone asks "is X a framework or a library?", the practical question to ask is: *who is in control of the flow?*

---

## 1.3 Why Frameworks Exist

Frameworks did not appear because developers like extra rules. They appeared because the same problems kept showing up in every project.

Early web applications were often built from scratch, with each team writing its own way of reading form data, matching URLs, talking to databases, and defending against attacks. This caused predictable trouble:

- **Repeated effort.** Every project reinvented routing, user logins, and validation.
- **Hidden security holes.** Security is easy to get subtly wrong. Writing your own protection against common attacks means repeating mistakes that experienced developers already learned to avoid.
- **Inconsistent code.** Two developers solving the same problem in two different ways makes a codebase hard to understand.
- **Slow onboarding.** A new teammate had to learn a one-of-a-kind project structure before being useful.

Frameworks capture the accumulated experience of many developers. Common problems are solved once, carefully, and shared with everyone. In return, you agree to work within the framework's structure.

The core reasons frameworks exist can be summarized as:

1. **Structure**: a predictable place for everything, so projects are easy to navigate.
2. **Productivity**: skip the repetitive foundation and build features sooner.
3. **Consistency**: teams write similar code in a similar way.
4. **Safety**: well-tested, widely reviewed solutions to hard problems such as security.
5. **Community**: a large group of people who know the same tool, publish extensions, and answer questions.

---

## 1.4 Key Benefits and Trade-offs

Frameworks are powerful, but they are not free. A good developer understands both sides.

### Benefits

| Benefit | Why it matters |
|---|---|
| **Faster development** | Common features are ready to use |
| **Built-in best practices** | Security, structure, and testing support are part of the package |
| **Easier teamwork** | Everyone follows the same conventions |
| **Large ecosystem** | Plugins, tutorials, and answers are easy to find |
| **Maintainability** | Predictable structure makes code easier to change later |

### Trade-offs

| Trade-off | What it means |
|---|---|
| **Learning curve** | You must learn the framework's concepts and vocabulary before you are productive |
| **Lock-in** | Your code is shaped around the framework, so switching later is expensive |
| **Overhead** | Frameworks include features you may not need, which can add size or reduce performance |
| **Abstraction leaks** | When something goes wrong, you may need to understand what the framework does under the hood |
| **Upgrade churn** | Major versions can introduce breaking changes that require rewriting parts of your code |
| **Over-engineering** | A large framework can be too heavy for a very small project |

### When a framework may *not* be the right choice

- A single static page or very small script rarely needs one.
- A learning exercise where the goal is to understand the basics may be better done without one.
- A project with highly unusual requirements may fight against the framework's conventions.

A good rule of thumb: **use a framework when the structure it provides saves you more work than it costs you to learn and follow it.** For almost every real application, it does.

---

[Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[2]-Types-Of-Frameworks.md)
