[⬅ Back to README](../../../README.md)

# JavaScript

Welcome! This is a self-paced course for learning JavaScript, the dynamic, multi-paradigm programming language of the web — running in every browser and, via Node.js, on servers, desktops, and mobile devices too.

---

## What is JavaScript?

JavaScript lets you:
- Build interactive, dynamic websites and single-page applications
- Manipulate web pages in real time through the DOM
- Build full-stack applications with Node.js on the backend
- Build REST and GraphQL APIs, real-time apps, and microservices
- Work with files, databases, and external APIs
- Organize code using functions, classes, modules, and closures
- Handle asynchronous operations with promises, async/await, and event loops
- Build mobile apps (React Native), desktop apps (Electron), and CLI tools
- Communicate over networks with WebSockets, HTTP, and messaging systems
- Test, bundle, optimize, and deploy production-ready web applications

## Table of Contents

**Getting Started**  

1. **[Installing Node.js, npm & First-Time Setup](./[1]-Installation-and-Setup.md)**  
    1.1 What Is Node.js And Why You Need It  
    1.2 Installing Node.js  
    1.3 Verifying Your Installation  
    1.4 Choosing A Code Editor  
    1.5 Your First Script File  
2. **[Running Code: Browser Console, Node REPL & Script Files](./[2]-Running-JavaScript-Code.md)**  
    2.1 The Browser Console  
    2.2 The Node REPL  
    2.3 Running Script Files  
    2.4 Running Code In A Browser Page  
    2.5 Comments  
3. **[How JavaScript Works: the Engine, Call Stack & Event Loop](./[3]-How-JavaScript-Works.md)**  
    3.1 The JavaScript Engine  
    3.2 JavaScript Is Single-Threaded  
    3.3 The Call Stack  
    3.4 The Event Loop (A First Look)  
    3.5 Interpreted, Then Compiled  
4. **[Package Management (npm, yarn, pnpm) & package.json](./[4]-Package-Management.md)**  
    4.1 What Is A Package?  
    4.2 Initializing A Project With package.json  
    4.3 Installing Packages  
    4.4 Using An Installed Package  
    4.5 package-lock.json And Reproducible Installs  
    4.6 Alternative Package Managers: Yarn And pnpm  

**Core Syntax**  

5. **[Variables & Data Types (var, let, const, typeof)](./%5B5%5D-Variables-and-Data-Types.md)**  
    5.1 Declaring Variables: let, const, var  
    5.2 Naming Rules And Conventions  
    5.3 The Primitive Data Types  
    5.4 undefined vs. null  
    5.5 Checking A Type With typeof  
6. **[Numbers, Strings & Booleans](./%5B6%5D-Numbers-Strings-and-Booleans.md)**  
    6.1 Numbers  
    6.2 Strings  
    6.3 Booleans  
    6.4 Type Coercion  
    6.5 Explicit Type Conversion  
7. **[Operators & Expressions (arithmetic, comparison, logical, ternary, nullish coalescing, optional chaining)](./%5B7%5D-Operators-and-Expressions.md)**  
    7.1 Arithmetic Operators  
    7.2 Assignment Operators  
    7.3 Comparison Operators  
    7.4 Logical Operators  
    7.5 Ternary Operator  
    7.6 Nullish Coalescing And Optional Chaining  
8. **[Conditionals: if, else if, else, switch](./%5B8%5D-Conditionals.md)**  
    8.1 if, else if, else  
    8.2 Truthy And Falsy In Conditions  
    8.3 The switch Statement  
    8.4 Nesting And Combining Conditions  
    8.5 The Ternary Operator As A Compact Conditional  
9. **[Loops: for, while, do-while, for...in, for...of, break, continue](./%5B9%5D-Loops.md)**  
    9.1 The for Loop  
    9.2 The while And do-while Loops  
    9.3 for...of — Looping Over Values  
    9.4 for...in — Looping Over Keys  
    9.5 break And continue  
    9.6 Nested Loops  
10. **[Functions & Scope (declarations, expressions, arrow functions, hoisting, closures)](./%5B10%5D-Functions-and-Scope.md)**  
    10.1 Function Declarations  
    10.2 Function Expressions And Arrow Functions  
    10.3 Default And Rest Parameters  
    10.4 Hoisting  
    10.5 Scope: Where Variables Live  
    10.6 Closures  
11. **[String Formatting & Manipulation (template literals, methods, regex basics)](./[11]-String-Formatting.md)**  
    11.1 Template Literals  
    11.2 Common String Methods  
    11.3 Slicing And Splitting  
    11.4 Joining Strings  
    11.5 Regex Basics  
12. **[Error Handling: try, catch, finally, throw, custom errors](./%5B12%5D-Error-Handling.md)**  
    12.1 What Happens When Code Throws  
    12.2 try, catch, finally  
    12.3 The throw Statement  
    12.4 Built-in Error Types  
    12.5 Custom Error Classes  

**Data Structures**  

13. **[Arrays & Array Methods (map, filter, reduce, and more)](./[13]-Arrays-and-Array-Methods.md)**  
    13.1 Creating And Accessing Arrays  
    13.2 Adding And Removing Items  
    13.3 Finding And Checking Items  
    13.4 Transforming Arrays: map, filter, reduce  
    13.5 Iterating: forEach vs. for...of  
    13.6 Sorting And Reversing  
    13.7 Other Useful Methods  
14. **[Objects & Object Methods](./[14]-Objects-and-Object-Methods.md)**  
    14.1 Creating And Accessing Objects  
    14.2 Adding, Updating, And Deleting Properties  
    14.3 Methods: Functions Inside Objects  
    14.4 Nested Objects  
    14.5 Checking Properties  
    14.6 Useful Object Static Methods  
    14.7 Shorthand Property And Method Syntax  
15. **[Destructuring, Spread & Rest Operators](./[15]-Destructuring-Spread-and-Rest.md)**  
    15.1 Array Destructuring  
    15.2 Object Destructuring  
    15.3 The Spread Operator (...)  
    15.4 The Rest Parameter (...)  
16. **[Sets & Maps (Set, Map, WeakSet, WeakMap)](./[16]-Sets-and-Maps.md)**  
    16.1 What Is A Set?  
    16.2 Iterating A Set  
    16.3 What Is A Map?  
    16.4 Iterating A Map  
    16.5 Map vs. Plain Object  
    16.6 WeakSet And WeakMap  
17. **[JSON: Parsing & Stringifying](./[17]-JSON.md)**  
    17.1 What Is JSON?  
    17.2 Converting A JavaScript Value To JSON  
    17.3 Converting JSON Back To A JavaScript Value  
    17.4 Deep Cloning With JSON  
    17.5 Where JSON Is Used  

**Object-Oriented Programming**  

18. **[Classes & Objects](./[18]-OOP-Classes-and-Objects.md)**  
    18.1 Why Object-Oriented Programming?  
    18.2 Defining A Class  
    18.3 Creating Instances  
    18.4 Object Literals vs. Classes  
    18.5 Methods Can Use Other Properties And Methods  
    18.6 Class Fields (Properties Without A Constructor)  
19. **[Prototypes & Prototypal Inheritance](./[19]-Prototypes-and-Prototypal-Inheritance.md)**  
    19.1 Classes Are "Syntactic Sugar"  
    19.2 What Is A Prototype?  
    19.3 How Classes Use Prototypes  
    19.4 The Pre-Class Pattern: Constructor Functions  
    19.5 Checking The Prototype Chain  
20. **[Inheritance & Polymorphism (`extends`, `super`)](./[20]-Inheritance-and-Polymorphism.md)**  
    20.1 What Is Inheritance?  
    20.2 The super Keyword  
    20.3 Overriding Methods  
    20.4 Polymorphism  
    20.5 When To Use Inheritance  
21. **[Encapsulation (private fields, closures, getters/setters)](./[21]-Encapsulation.md)**  
    21.1 What Is Encapsulation?  
    21.2 Private Fields  
    21.3 Private Methods  
    21.4 Getters And Setters  
    21.5 Encapsulation Through Closures (The Pre-# Pattern)  
22. **[Static Members & the `this` Keyword](./[22]-Static-Members-and-This.md)**  
    22.1 Static Properties And Methods  
    22.2 A Common Use: Factory Methods  
    22.3 What `this` Refers To  
    22.4 `this` Inside Regular Functions vs. Arrow Functions  
    22.5 Explicitly Controlling `this`: call, apply, bind  
23. **[Mixins & Composition](./[23]-Mixins-and-Composition.md)**  
    23.1 The Limits Of Single Inheritance  
    23.2 What Is A Mixin?  
    23.3 Composition Over Inheritance  
    23.4 Composition With Object Spread  
    23.5 Choosing Between Inheritance, Mixins, And Composition  

**Asynchronous JavaScript**  
    24. **[Callbacks & the Callback Pattern](./[24]-Callbacks.md)**  
    25. **[Promises](./[25]-Promises.md)**  
    26. **[async/await](./[26]-Async-Await.md)**  
    27. **[The Event Loop, Microtasks & Macrotasks in Depth](./[27]-Event-Loop-Deep-Dive.md)**  
    28. **[Timers (setTimeout, setInterval, requestAnimationFrame)](./[28]-Timers.md)**  

**Modules & Advanced Language Features**  
    29. **[Modules: ES Modules (import/export) & CommonJS (require)](./[29]-Modules.md)**  
    30. **[Iterators & Generators (yield, Symbol.iterator)](./[30]-Iterators-and-Generators.md)**  
    31. **[Symbols](./[31]-Symbols.md)**  
    32. **[Proxies & Reflect](./[32]-Proxies-and-Reflect.md)**  
    33. **[Functional Programming (pure functions, currying, composition)](./[33]-Functional-Programming.md)**  
    34. **[Memory Management & Garbage Collection](./[34]-Memory-Management.md)**  
    35. **[TypeScript Fundamentals (types, interfaces, generics)](./[35]-TypeScript-Fundamentals.md)**  

**Browser & DOM**  
    36. **[The DOM: Selecting & Manipulating Elements](./[36]-DOM-Manipulation.md)**  
    37. **[Events & Event Handling (bubbling, capturing, delegation)](./[37]-Events-and-Event-Handling.md)**  
    38. **[Forms & Form Validation](./[38]-Forms-and-Validation.md)**  
    39. **[Browser Storage (localStorage, sessionStorage, cookies, IndexedDB)](./[39]-Browser-Storage.md)**  
    40. **[Browser APIs (Fetch, Geolocation, Notifications, Clipboard, Intersection Observer)](./[40]-Browser-APIs.md)**  
    41. **[Web Workers & Service Workers](./[41]-Web-Workers-and-Service-Workers.md)**  
    42. **[Progressive Web Apps (PWAs)](./[42]-Progressive-Web-Apps.md)**  

**Standard Library & Built-ins**  
    43. **[Working with Dates & Times (Date, Intl, Temporal proposal)](./[43]-Dates-and-Times.md)**  
    44. **[Regular Expressions](./[44]-Regular-Expressions.md)**  
    45. **[Math & Number Utilities](./[45]-Math-and-Number-Utilities.md)**  
    46. **[Internationalization (Intl API, i18next)](./[46]-Internationalization.md)**  

**Networking**  
    47. **[Making HTTP Requests (Fetch API, Axios, XMLHttpRequest)](./[47]-HTTP-Requests.md)**  
    48. **[WebSockets](./[48]-WebSockets.md)**  
    49. **[Server-Sent Events (SSE)](./[49]-Server-Sent-Events.md)**  
    50. **[GraphQL with JavaScript (Apollo, urql)](./[50]-GraphQL.md)**  
    51. **[gRPC & Protocol Buffers with Node.js](./[51]-gRPC-and-Protocol-Buffers.md)**  

**Security**  
    52. **[Web Security Fundamentals (XSS, CSRF, CORS, Content Security Policy)](./[52]-Web-Security-Fundamentals.md)**  
    53. **[Hashing & Encryption (Web Crypto API, bcrypt, Node crypto module)](./[53]-Hashing-and-Encryption.md)**  
    54. **[Authentication & Authorization (JWT, OAuth2, sessions, Passport.js)](./[54]-Authentication-and-Authorization.md)**  
    55. **[Secure Coding Practices](./[55]-Secure-Coding-Practices.md)**  

**Node.js & Backend Development**  
    56. **[Node.js Fundamentals (modules, global objects, process)](./[56]-Nodejs-Fundamentals.md)**  
    57. **[File System Operations (fs module, streams)](./[57]-File-System-Operations.md)**  
    58. **[Streams & Buffers](./[58]-Streams-and-Buffers.md)**  
    59. **[Building CLI Tools (process.argv, Commander, Yargs, Inquirer)](./[59]-CLI-Tools.md)**  
    60. **[Environment Variables & Configuration (dotenv, config management)](./[60]-Environment-Variables-and-Configuration.md)**  
    61. **[Express.js](./[61]-Express.md)**  
    62. **[NestJS](./[62]-NestJS.md)**  
    63. **[Koa & Fastify](./[63]-Koa-and-Fastify.md)**  
    64. **[Building REST APIs](./[64]-REST-APIs.md)**  
    65. **[API Documentation (OpenAPI/Swagger)](./[65]-API-Documentation-OpenAPI.md)**  
    66. **[Middleware Patterns](./[66]-Middleware-Patterns.md)**  
    67. **[Templating Engines (EJS, Pug, Handlebars)](./[67]-Templating-Engines.md)**  
    68. **[Server-Side Rendering Fundamentals](./[68]-Server-Side-Rendering.md)**  

**Databases**  
    69. **[Working with SQL Databases (node-postgres, mysql2)](./[69]-SQL-Databases.md)**  
    70. **[ORMs & Query Builders (Prisma, Sequelize, TypeORM, Knex)](./[70]-ORMs-and-Query-Builders.md)**  
    71. **[MongoDB with Mongoose](./[71]-MongoDB-and-Mongoose.md)**  
    72. **[Caching with Redis](./[72]-Caching-with-Redis.md)**  

**Messaging & Event-Driven Systems**  
    73. **[Node.js EventEmitter & Pub/Sub Patterns](./[73]-EventEmitter-and-PubSub.md)**  
    74. **[Message Queues (RabbitMQ, BullMQ)](./[74]-Message-Queues.md)**  
    75. **[Apache Kafka with Node.js (KafkaJS)](./[75]-Apache-Kafka.md)**  

**Frontend Frameworks & Libraries**  
    76. **[React Fundamentals (components, JSX, props, state)](./[76]-React-Fundamentals.md)**  
    77. **[React Hooks (useState, useEffect, custom hooks)](./[77]-React-Hooks.md)**  
    78. **[State Management (Redux, Zustand, Context API)](./[78]-State-Management.md)**  
    79. **[React Routing (React Router)](./[79]-React-Routing.md)**  
    80. **[Next.js (SSR, SSG, App Router)](./[80]-Nextjs.md)**  
    81. **[Vue.js Fundamentals](./[81]-Vuejs-Fundamentals.md)**  
    82. **[Angular Fundamentals](./[82]-Angular-Fundamentals.md)**  
    83. **[Svelte & SvelteKit](./[83]-Svelte-and-SvelteKit.md)**  

**Web Scraping & Automation**  
    84. **[Browser Automation with Puppeteer & Playwright](./[84]-Puppeteer-and-Playwright.md)**  
    85. **[Web Scraping (Cheerio, jsdom)](./[85]-Web-Scraping.md)**  
    86. **[Task Scheduling (node-cron, node-schedule)](./[86]-Task-Scheduling.md)**  

**Mobile & Desktop Development**  
    87. **[React Native Fundamentals](./[87]-React-Native-Fundamentals.md)**  
    88. **[Electron for Desktop Apps](./[88]-Electron.md)**  

**Design Patterns & Architecture**  
    89. **[SOLID Principles in JavaScript](./[89]-SOLID-Principles.md)**  
    90. **[Creational Patterns (Singleton, Factory, Builder)](./[90]-Creational-Design-Patterns.md)**  
    91. **[Structural Patterns (Adapter, Decorator, Facade)](./[91]-Structural-Design-Patterns.md)**  
    92. **[Behavioral Patterns (Observer, Strategy, Command)](./[92]-Behavioral-Design-Patterns.md)**  
    93. **[Architectural Patterns (MVC, Flux, Component-Based Architecture)](./[93]-Architectural-Patterns.md)**  

**Testing & Code Quality**  
    94. **[Testing Fundamentals: Jest & Vitest](./[94]-Testing-Jest-and-Vitest.md)**  
    95. **[Mocking & Spies](./[95]-Mocking-and-Spies.md)**  
    96. **[End-to-End Testing (Cypress, Playwright Test)](./[96]-End-to-End-Testing.md)**  
    97. **[Linters & Formatters (ESLint, Prettier)](./[97]-Linters-and-Formatters.md)**  
    98. **[Code Coverage (Istanbul/nyc)](./[98]-Code-Coverage.md)**  

**Build Tools & Bundlers**  
    99. **[Bundlers (Webpack, Vite, esbuild, Rollup)](./[99]-Bundlers.md)**  
    100. **[Transpilers (Babel) & Polyfills](./[100]-Babel-and-Polyfills.md)**  
    101. **[Monorepo Tools (Turborepo, Nx, Lerna)](./[101]-Monorepo-Tools.md)**  

**Packaging & Deployment**  
    102. **[Publishing npm Packages](./[102]-Publishing-npm-Packages.md)**  
    103. **[Docker Basics for Node.js Apps](./[103]-Docker-Basics.md)**  
    104. **[Kubernetes Fundamentals for Node.js Services](./[104]-Kubernetes-Fundamentals.md)**  
    105. **[Serverless JavaScript (AWS Lambda, Vercel, Cloudflare Workers)](./[105]-Serverless-JavaScript.md)**  
    106. **[CI/CD Basics (GitHub Actions)](./[106]-CICD-Basics.md)**  
    107. **[Observability: Logging, Metrics & Tracing](./[107]-Observability.md)**  

**Performance & Optimization**  
    108. **[Performance Profiling (Chrome DevTools, Node --prof)](./[108]-Performance-Profiling.md)**  
    109. **[Web Performance & Core Web Vitals](./[109]-Web-Performance-and-Core-Web-Vitals.md)**  
    110. **[Lazy Loading & Code Splitting](./[110]-Lazy-Loading-and-Code-Splitting.md)**  

**Best Practices**  
    111. **[Idiomatic JavaScript & Style Guides (Airbnb, Standard)](./[111]-Best-Practices-and-Style.md)**