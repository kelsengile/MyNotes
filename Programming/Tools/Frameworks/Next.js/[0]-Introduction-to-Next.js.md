[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to JavaScript](/Programming/Syntax/JavaScript/[0]-Introduction-to-JavaScript.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nextjs/nextjs-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Next.js</h1>
</div>


Next.js is a full-stack React framework that adds the pieces React leaves out: file-based routing, multiple rendering strategies, data loading, API endpoints, and production optimizations. It lets you build both the frontend and backend of an application in a single project.

Next.js is a popular choice for SEO-friendly sites and production-grade web apps because pages can be rendered on the server or at build time. This Topic focuses on the App Router, covering routing, Server and Client Components, data fetching, server actions, caching, authentication, and deployment.

### Why Study Next.js?

- **It solves React's missing pieces** — routing, rendering, and optimization are built in rather than assembled by hand.
- **Great for SEO and performance** — server rendering and static generation deliver fast, indexable pages.
- **Frontend and backend in one place** — route handlers and server actions remove the need for a separate API in many apps.
- **Industry standard for React apps** — most new production React projects start with Next.js.

### Next.js At A Glance

| Attribute | Details |
|---|---|
| Language | JavaScript / TypeScript |
| Type | Full-stack React meta-framework |
| Created by | Vercel |
| Best for | SEO-friendly sites, full-stack web apps |
| Pairs well with | React, Tailwind CSS, Prisma, Vercel |

---

### Table of Contents

**Basics**

   1. **[What Is Next.js](./[1]-What-Is-Nextjs.md)**  
       1.1 Meta-frameworks explained  
       1.2 Next.js vs plain React  
       1.3 Creating a project with create-next-app  
       1.4 Tour of the project structure  
   2. **[Routing With The App Router](./[2]-Routing-With-The-App-Router.md)**  
       2.1 File-based routing  
       2.2 Layouts and templates  
       2.3 Dynamic routes  
       2.4 Route groups and parallel routes  
   3. **[Navigation And Special Files](./[3]-Navigation-And-Special-Files.md)**  
       3.1 The Link component and navigation hooks  
       3.2 loading.js and streaming UI  
       3.3 error.js and not-found.js  
       3.4 Redirects  
   4. **[Server And Client Components](./[4]-Server-And-Client-Components.md)**  
       4.1 Server Components by default  
       4.2 The use client boundary  
       4.3 Passing data between them  
       4.4 Composition patterns  

**Data And Rendering**

   5. **[Data Fetching](./[5]-Data-Fetching.md)**  
       5.1 Fetching on the server  
       5.2 Parallel and sequential requests  
       5.3 Suspense and streaming  
       5.4 Fetching in Client Components  
   6. **[Rendering Strategies](./[6]-Rendering-Strategies.md)**  
       6.1 Static rendering  
       6.2 Dynamic rendering  
       6.3 Incremental static regeneration  
       6.4 Partial prerendering  
   7. **[Caching And Revalidation](./[7]-Caching-And-Revalidation.md)**  
       7.1 The caching layers  
       7.2 Time-based revalidation  
       7.3 On-demand revalidation  
       7.4 Opting out of caching  
   8. **[Server Actions And Forms](./[8]-Server-Actions-And-Forms.md)**  
       8.1 Defining server actions  
       8.2 Handling forms  
       8.3 Pending and optimistic UI  
       8.4 Validation and errors  
   9. **[Route Handlers And APIs](./[9]-Route-Handlers-And-APIs.md)**  
       9.1 Creating route handlers  
       9.2 HTTP methods and responses  
       9.3 Working with request data  
       9.4 When to use handlers vs actions  

**Building Features**

   10. **[Styling In Next.js](./[10]-Styling-In-Nextjs.md)**  
       10.1 CSS Modules  
       10.2 Tailwind CSS  
       10.3 CSS-in-JS considerations  
       10.4 Global styles and fonts  
   11. **[Optimization And Metadata](./[11]-Optimization-And-Metadata.md)**  
       11.1 next/image  
       11.2 next/font  
       11.3 Metadata and SEO  
       11.4 Scripts and third-party code  
   12. **[Middleware And Edge](./[12]-Middleware-And-Edge.md)**  
       12.1 What middleware does  
       12.2 Matching requests  
       12.3 Redirects and rewrites  
       12.4 Edge vs Node runtime  
   13. **[Databases And ORMs](./[13]-Databases-And-ORMs.md)**  
       13.1 Connecting to a database  
       13.2 Prisma and Drizzle  
       13.3 Querying in Server Components  
       13.4 Migrations  
   14. **[Authentication And Authorization](./[14]-Authentication-And-Authorization.md)**  
       14.1 Sessions and tokens  
       14.2 Auth.js  
       14.3 Protecting routes  
       14.4 Role-based access  

**Quality And Delivery**

   15. **[TypeScript With Next.js](./[15]-TypeScript-With-Nextjs.md)**  
       15.1 Typed routes and params  
       15.2 Typing props and actions  
       15.3 Environment typing  
       15.4 Strictness settings  
   16. **[Testing Next.js Applications](./[16]-Testing-Nextjs-Applications.md)**  
       16.1 Unit testing components  
       16.2 Testing server code  
       16.3 End-to-end tests  
       16.4 Mocking requests  
   17. **[Performance Tuning](./[17]-Performance-Tuning.md)**  
       17.1 Core Web Vitals  
       17.2 Bundle analysis  
       17.3 Lazy loading  
       17.4 Monitoring  
   18. **[Deployment](./[18]-Deployment.md)**  
       18.1 Deploying on Vercel  
       18.2 Self-hosting with Node and Docker  
       18.3 Static exports  
       18.4 Environment variables and secrets  
   19. **[Next Steps With Next.js](./[19]-Next-Steps-With-Nextjs.md)**  
       19.1 The Pages Router and migration  
       19.2 Internationalization  
       19.3 Monorepos  
       19.4 Project ideas
