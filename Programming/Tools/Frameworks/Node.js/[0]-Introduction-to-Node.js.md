[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to JavaScript](/Programming/Syntax/JavaScript/[0]-Introduction-to-JavaScript.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nodejs/nodejs-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Node.js</h1>
</div>


Node.js is a JavaScript runtime built on Chrome's V8 engine that lets you run JavaScript outside the browser. It powers servers, APIs, command-line tools, build systems, and real-time applications, all with the same language you use on the frontend.

Node.js is not a framework; it is the foundation that frameworks like Express and NestJS are built on. This Topic teaches how Node.js actually works: its event loop, modules, built-in APIs for files, streams, and networking, and the tooling and practices needed to build and ship reliable programs.

Download: [nodejs.org/en/download](https://nodejs.org/en/download)

### Why Study Node.js?

- **It is the backbone of modern JavaScript tooling** — npm, bundlers, linters, and test runners all run on Node.js.
- **One language across the stack** — share skills and sometimes code between frontend and backend.
- **Great at I/O-heavy work** — its non-blocking model handles many concurrent connections efficiently.
- **It makes every Node-based framework easier** — understanding the runtime explains Express, NestJS, and Next.js behavior.

### Node.js At A Glance

| Attribute | Details |
|---|---|
| Language | JavaScript / TypeScript |
| Type | Runtime |
| Created by | Ryan Dahl (now OpenJS Foundation) |
| Best for | APIs, CLI tools, real-time apps, tooling |
| Pairs well with | Express, NestJS, npm, Docker |

---

### Table of Contents

**Basics**

   1. **[What Is Node.js](./[1]-What-Is-Nodejs.md)**  
       1.1 Runtime vs browser  
       1.2 V8 and libuv  
       1.3 Installing Node.js and version managers  
       1.4 Running scripts and the REPL  
   2. **[JavaScript For Node](./[2]-JavaScript-For-Node.md)**  
       2.1 Globals and the process object  
       2.2 CommonJS modules  
       2.3 ES modules  
       2.4 Differences from browser JavaScript  
   3. **[Modules And npm](./[3]-Modules-And-npm.md)**  
       3.1 Using built-in modules  
       3.2 Installing packages  
       3.3 Creating your own modules  
       3.4 Publishing basics  

**Asynchronous Node**

   4. **[The Event Loop](./[4]-The-Event-Loop.md)**  
       4.1 Single-threaded model  
       4.2 Event loop phases  
       4.3 Microtasks and nextTick  
       4.4 Blocking vs non-blocking code  
   5. **[Asynchronous Patterns](./[5]-Asynchronous-Patterns.md)**  
       5.1 Callbacks and callback hell  
       5.2 Promises  
       5.3 async and await  
       5.4 Error handling in async code  
   6. **[Events And EventEmitter](./[6]-Events-And-EventEmitter.md)**  
       6.1 The observer pattern  
       6.2 Emitting and listening  
       6.3 Once and error events  
       6.4 Building event-driven code  

**Core APIs**

   7. **[The File System](./[7]-The-File-System.md)**  
       7.1 Reading and writing files  
       7.2 Directories and metadata  
       7.3 Sync vs async vs promises APIs  
       7.4 Watching files  
   8. **[Paths, OS, And Process](./[8]-Paths-OS-And-Process.md)**  
       8.1 The path module  
       8.2 The os module  
       8.3 Command-line arguments  
       8.4 Exit codes and signals  
   9. **[Buffers And Streams](./[9]-Buffers-And-Streams.md)**  
       9.1 Binary data and Buffers  
       9.2 Readable and writable streams  
       9.3 Piping and backpressure  
       9.4 Transform streams  
   10. **[HTTP And Networking](./[10]-HTTP-And-Networking.md)**  
       10.1 The http module  
       10.2 Building a server from scratch  
       10.3 Making HTTP requests  
       10.4 HTTPS, TCP, and WebSockets overview  
   11. **[Child Processes And Worker Threads](./[11]-Child-Processes-And-Worker-Threads.md)**  
       11.1 Spawning processes  
       11.2 The cluster module  
       11.3 Worker threads  
       11.4 Choosing a concurrency model  

**Tooling And Projects**

   12. **[Package Management](./[12]-Package-Management.md)**  
       12.1 package.json explained  
       12.2 Scripts and lock files  
       12.3 npm, yarn, and pnpm  
       12.4 Semantic versioning  
   13. **[Environment And Configuration](./[13]-Environment-And-Configuration.md)**  
       13.1 Environment variables  
       13.2 dotenv and config files  
       13.3 Separating dev and production  
       13.4 Handling secrets  
   14. **[Working With Databases](./[14]-Working-With-Databases.md)**  
       14.1 Connecting to SQL and NoSQL  
       14.2 Drivers vs ORMs  
       14.3 Connection pooling  
       14.4 Basic CRUD example  
   15. **[Building Command-Line Tools](./[15]-Building-Command-Line-Tools.md)**  
       15.1 Parsing arguments  
       15.2 Interactive prompts  
       15.3 Output formatting  
       15.4 Distributing a CLI  

**Quality And Delivery**

   16. **[Debugging And Logging](./[16]-Debugging-And-Logging.md)**  
       16.1 The built-in inspector  
       16.2 Debugging in VS Code  
       16.3 Structured logging  
       16.4 Error handling strategy  
   17. **[Testing Node.js Applications](./[17]-Testing-Nodejs-Applications.md)**  
       17.1 The built-in test runner  
       17.2 Jest and Vitest  
       17.3 Mocking and stubbing  
       17.4 Testing async code  
   18. **[Security And Performance](./[18]-Security-And-Performance.md)**  
       18.1 Dependency auditing  
       18.2 Common vulnerabilities  
       18.3 Profiling and memory leaks  
       18.4 Caching basics  
   19. **[Deployment](./[19]-Deployment.md)**  
       19.1 Production process managers  
       19.2 Docker containers  
       19.3 Serverless options  
       19.4 Monitoring basics  
   20. **[Next Steps With Node.js](./[20]-Next-Steps-With-Nodejs.md)**  
       20.1 Express and NestJS  
       20.2 TypeScript with Node  
       20.3 Microservices and queues  
       20.4 Building real projects
