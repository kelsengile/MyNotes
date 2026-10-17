[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to TypeScript](/Programming/Syntax/TypeScript/[0]-Introduction-to-TypeScript.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nestjs/nestjs-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">NestJS</h1>
</div>


NestJS is an opinionated, TypeScript-first Node.js framework for building scalable server-side applications. It runs on top of Express (or Fastify) and adds an Angular-inspired architecture of modules, controllers, providers, and dependency injection.

This structure keeps large back ends organized and testable. This Topic covers the TypeScript foundations Nest relies on, its core building blocks, the request pipeline, databases, authentication, GraphQL, WebSockets, microservices, testing, and deployment.

### Why Study NestJS?

- **Structure at scale** — modules and dependency injection keep large codebases organized.
- **TypeScript throughout** — strong typing catches errors early and improves tooling.
- **Batteries available** — official packages for config, GraphQL, queues, caching, and OpenAPI.
- **Familiar to Angular developers** — the architecture and decorators will feel natural if you know Angular.

### NestJS At A Glance

| Attribute | Details |
|---|---|
| Language | TypeScript |
| Type | Backend framework |
| Runs on | Node.js (Express or Fastify) |
| Best for | Scalable APIs and enterprise back ends |
| Pairs well with | TypeORM, Prisma, PostgreSQL, Docker |

---

### Table of Contents

**Basics**

   1. **[What Is NestJS](./[1]-What-Is-NestJS.md)**  
       1.1 Why Nest exists  
       1.2 Nest vs Express  
       1.3 Installing the Nest CLI  
       1.4 Tour of a generated project  
   2. **[TypeScript And Decorators](./[2]-TypeScript-And-Decorators.md)**  
       2.1 Classes and types  
       2.2 How decorators work  
       2.3 Metadata and reflection  
       2.4 Why Nest depends on them  
   3. **[Modules](./[3]-Modules.md)**  
       3.1 The module system  
       3.2 Feature modules  
       3.3 Shared and global modules  
       3.4 Dynamic modules  
   4. **[Controllers And Routing](./[4]-Controllers-And-Routing.md)**  
       4.1 Defining controllers  
       4.2 Route handlers and decorators  
       4.3 Parameters, queries, and bodies  
       4.4 Status codes and responses  
   5. **[Providers And Dependency Injection](./[5]-Providers-And-Dependency-Injection.md)**  
       5.1 Services as providers  
       5.2 Constructor injection  
       5.3 Provider scopes  
       5.4 Custom providers  

**The Request Pipeline**

   6. **[Middleware](./[6]-Middleware.md)**  
       6.1 Function and class middleware  
       6.2 Applying middleware  
       6.3 Global middleware  
       6.4 Use cases  
   7. **[Pipes And Validation](./[7]-Pipes-And-Validation.md)**  
       7.1 Built-in pipes  
       7.2 DTOs  
       7.3 class-validator and class-transformer  
       7.4 Global validation  
   8. **[Guards And Interceptors](./[8]-Guards-And-Interceptors.md)**  
       8.1 Guards for access control  
       8.2 Interceptors for transformation  
       8.3 Execution context  
       8.4 Combining them  
   9. **[Exception Filters](./[9]-Exception-Filters.md)**  
       9.1 Built-in exceptions  
       9.2 Custom exceptions  
       9.3 Exception filters  
       9.4 Consistent error responses  
   10. **[Request Lifecycle](./[10]-Request-Lifecycle.md)**  
       10.1 Order of execution  
       10.2 Where each piece fits  
       10.3 Debugging the pipeline  
       10.4 Design guidelines  

**Data And Configuration**

   11. **[Configuration And Environments](./[11]-Configuration-And-Environments.md)**  
       11.1 The config module  
       11.2 Validating environment variables  
       11.3 Per-environment settings  
       11.4 Secrets handling  
   12. **[Databases With TypeORM](./[12]-Databases-With-TypeORM.md)**  
       12.1 Entities and repositories  
       12.2 Relations  
       12.3 Migrations  
       12.4 Transactions  
   13. **[Prisma And Mongoose](./[13]-Prisma-And-Mongoose.md)**  
       13.1 Using Prisma in Nest  
       13.2 Using Mongoose for MongoDB  
       13.3 Choosing a data layer  
       13.4 Testing data access  

**Security**

   14. **[Authentication With Passport And JWT](./[14]-Authentication-With-Passport-And-JWT.md)**  
       14.1 Local strategy  
       14.2 JWT strategy  
       14.3 Login and token flow  
       14.4 Refresh tokens  
   15. **[Authorization And Roles](./[15]-Authorization-And-Roles.md)**  
       15.1 Role-based access control  
       15.2 Custom decorators  
       15.3 Policies and permissions  
       15.4 Protecting routes  
   16. **[API Security](./[16]-API-Security.md)**  
       16.1 CORS and Helmet  
       16.2 Rate limiting  
       16.3 Input sanitization  
       16.4 Secure configuration  

**Advanced Features**

   17. **[OpenAPI And Documentation](./[17]-OpenAPI-And-Documentation.md)**  
       17.1 The Swagger module  
       17.2 Decorating endpoints  
       17.3 DTO documentation  
       17.4 Generating clients  
   18. **[GraphQL With NestJS](./[18]-GraphQL-With-NestJS.md)**  
       18.1 Code-first vs schema-first  
       18.2 Resolvers  
       18.3 Queries and mutations  
       18.4 Dataloaders  
   19. **[WebSockets And Real-Time](./[19]-WebSockets-And-Real-Time.md)**  
       19.1 Gateways  
       19.2 Events and rooms  
       19.3 Authentication for sockets  
       19.4 Scaling considerations  
   20. **[Microservices And Queues](./[20]-Microservices-And-Queues.md)**  
       20.1 Transport layers  
       20.2 Message and event patterns  
       20.3 Queues with Bull  
       20.4 Scheduling and caching  

**Quality And Delivery**

   21. **[Testing NestJS Applications](./[21]-Testing-NestJS-Applications.md)**  
       21.1 Unit testing with Jest  
       21.2 The testing module  
       21.3 End-to-end testing  
       21.4 Mocking providers  
   22. **[Deployment](./[22]-Deployment.md)**  
       22.1 Building for production  
       22.2 Docker  
       22.3 Environment setup  
       22.4 Monitoring and health checks  
   23. **[Next Steps With NestJS](./[23]-Next-Steps-With-NestJS.md)**  
       23.1 Monorepo mode  
       23.2 CQRS and DDD patterns  
       23.3 Performance tuning  
       23.4 Project ideas
