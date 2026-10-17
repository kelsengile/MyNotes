[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to JavaScript](/Programming/Syntax/JavaScript/[0]-Introduction-to-JavaScript.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/express/express-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Express</h1>
</div>


Express is a minimal, flexible web framework for Node.js. It gives you a thin layer on top of Node's HTTP module, with routing, middleware, and helpers for requests and responses, while leaving every other decision up to you.

Because of that simplicity, Express has become the foundation of a large part of the Node.js ecosystem, from small REST APIs to production back ends. This Topic teaches routing, middleware, API design, validation, databases, authentication, security, testing, and deployment.

### Why Study Express?

- **Small enough to learn quickly** — a handful of concepts cover most of what you need.
- **Middleware teaches core backend ideas** — the request pipeline concept appears in nearly every framework.
- **Huge ecosystem** — thousands of compatible packages for auth, logging, uploads, and more.
- **A stepping stone** — knowing Express makes NestJS, Fastify, and other Node frameworks easier to pick up.

### Express At A Glance

| Attribute | Details |
|---|---|
| Language | JavaScript / TypeScript |
| Type | Minimal backend framework |
| Runs on | Node.js |
| Best for | REST APIs and lightweight web servers |
| Pairs well with | MongoDB, PostgreSQL, Prisma, Passport |

---

### Table of Contents

**Basics**

   1. **[What Is Express](./[1]-What-Is-Express.md)**  
       1.1 Where Express fits on Node.js  
       1.2 Express vs Fastify and Koa  
       1.3 Installing and your first server  
       1.4 Project layout  
   2. **[Routing Basics](./[2]-Routing-Basics.md)**  
       2.1 Route methods and paths  
       2.2 Route parameters and query strings  
       2.3 Route handlers  
       2.4 Route ordering  
   3. **[Requests And Responses](./[3]-Requests-And-Responses.md)**  
       3.1 The request object  
       3.2 Sending JSON and status codes  
       3.3 Redirects and headers  
       3.4 Cookies  
   4. **[Middleware](./[4]-Middleware.md)**  
       4.1 What middleware is  
       4.2 Built-in middleware  
       4.3 Writing custom middleware  
       4.4 Order of execution  
   5. **[Routers And Project Structure](./[5]-Routers-And-Project-Structure.md)**  
       5.1 express.Router  
       5.2 Splitting routes into files  
       5.3 Controllers and services  
       5.4 Configuration modules  

**Building APIs**

   6. **[Building REST APIs](./[6]-Building-REST-APIs.md)**  
       6.1 REST principles in Express  
       6.2 CRUD endpoints  
       6.3 Pagination and filtering  
       6.4 API versioning  
   7. **[Validation And Error Handling](./[7]-Validation-And-Error-Handling.md)**  
       7.1 Validating input with Zod or Joi  
       7.2 Error-handling middleware  
       7.3 Custom error classes  
       7.4 Handling async errors  
   8. **[Working With Databases](./[8]-Working-With-Databases.md)**  
       8.1 MongoDB with Mongoose  
       8.2 SQL with Prisma or Sequelize  
       8.3 Connection management  
       8.4 Structuring data access  
   9. **[Authentication](./[9]-Authentication.md)**  
       9.1 Password hashing  
       9.2 Sessions and cookies  
       9.3 JWT authentication  
       9.4 Passport.js  
   10. **[Views, Static Files, And Uploads](./[10]-Views-Static-Files-And-Uploads.md)**  
       10.1 Serving static files  
       10.2 Template engines like EJS and Pug  
       10.3 File uploads with Multer  
       10.4 Streaming responses  

**Hardening And Delivery**

   11. **[Security Best Practices](./[11]-Security-Best-Practices.md)**  
       11.1 Helmet and secure headers  
       11.2 CORS  
       11.3 Rate limiting  
       11.4 Input sanitization  
   12. **[Logging And Debugging](./[12]-Logging-And-Debugging.md)**  
       12.1 Request logging with Morgan  
       12.2 Application logging with Winston or Pino  
       12.3 Debugging techniques  
       12.4 Monitoring  
   13. **[Testing Express Applications](./[13]-Testing-Express-Applications.md)**  
       13.1 Unit tests with Jest or Vitest  
       13.2 Integration tests with Supertest  
       13.3 Mocking databases  
       13.4 Test organization  
   14. **[Real-Time And Advanced Features](./[14]-Real-Time-And-Advanced-Features.md)**  
       14.1 WebSockets with Socket.IO  
       14.2 Server-sent events  
       14.3 Caching with Redis  
       14.4 Background jobs  
   15. **[Deployment](./[15]-Deployment.md)**  
       15.1 Environment configuration  
       15.2 PM2 and process management  
       15.3 Docker  
       15.4 Reverse proxies and HTTPS  
   16. **[Next Steps With Express](./[16]-Next-Steps-With-Express.md)**  
       16.1 TypeScript with Express  
       16.2 Moving to NestJS  
       16.3 API documentation with OpenAPI  
       16.4 Project ideas
