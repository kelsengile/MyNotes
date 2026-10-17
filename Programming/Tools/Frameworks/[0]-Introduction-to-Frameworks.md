[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/react/react-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Frameworks</h1>
</div>


A framework is a pre-built foundation of code, conventions, and tools that gives your application a structure to grow on. Instead of writing routing, state handling, security, and project layout from scratch every time, you build on top of something that already solves those problems. This Topic builds up the fundamental, tool-agnostic concepts behind frameworks from the ground up: what they are, how they differ from libraries and runtimes, how they organize an application, and how to choose the right one for the job.

These lessons intentionally avoid tying the concepts to any single product. Once you understand the fundamentals here, head over to the tool-specific folders below to see how those concepts are put into practice with real, widely-used tools.

### Why Study Frameworks?

- **Almost every real project uses one** — web apps, mobile apps, and APIs are rarely built from nothing; they are built on a framework that handles the repetitive, error-prone parts.
- **They save time and prevent mistakes** — routing, security, and project structure come pre-solved, so you focus on your own features instead of reinventing the basics.
- **The concepts outlast the tool** — components, routing, state, middleware, and dependency management show up whether you're using React, Angular, Django, or something brand new next year.
- **It's a universal interview and job topic** — from junior developer to tech lead, framework knowledge is assumed in most software roles, and job listings are usually written around specific ones.

> **A quick note on terminology:** not every tool in this Topic is technically a "framework." **React** is a UI *library*, **Node.js** is a JavaScript *runtime* (Express and NestJS are the frameworks built on top of it), and **Flutter** is a UI *toolkit/framework* for building apps from a single codebase. In everyday conversation (and on job boards) all of these get grouped together as "frameworks," so we cover them side by side — and a later lesson explains exactly how they differ.

Here's a preview of the main framework families you'll compare in this course:

| Framework Type | What It Builds | Good Fit For | Example Tools |
|---|---|---|---|
| Frontend (Web UI) | Interactive user interfaces in the browser | Dashboards, single-page apps, websites | React, Angular, Vue.js, Svelte |
| Full-Stack (Meta-Frameworks) | Frontend + server rendering + routing in one | SEO-friendly sites, full web apps | Next.js |
| Backend (JavaScript) | Servers, APIs, and business logic | REST APIs, real-time services | Node.js, Express, NestJS |
| Backend (Other Languages) | Servers, APIs, and web apps | Rapid development, enterprise systems | Django, Flask, FastAPI, Spring Boot, Laravel, Ruby on Rails, ASP.NET Core |
| Styling (CSS) | The look and layout of user interfaces | Fast, consistent, responsive designs without writing much custom CSS | Tailwind CSS |
| Mobile / Cross-Platform | iOS and Android apps from one codebase | Mobile apps without maintaining two separate codebases | Flutter, React Native |

### Framework Tools:

The lessons above introduce the fundamentals of frameworks, application structure, and how frameworks fit into the wider software ecosystem. To see those concepts applied to specific, widely-used frameworks and platforms, continue on to:

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>

<tr>
<td align="center" width="25%" height="100"><a href="./React/[0]-Introduction-to-React.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/react/react-original.svg" width="40" title="React"/><br>React</a></td>
<td align="left" width="75%">A popular JavaScript library for building user interfaces out of reusable components, widely used for single-page applications and the foundation of many larger frameworks.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Node.js/[0]-Introduction-to-Node.js.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nodejs/nodejs-original.svg" width="40" title="Node.js"/><br>Node.js</a></td>
<td align="left" width="75%">A JavaScript runtime that lets you run JavaScript outside the browser, making it possible to build servers, APIs, command-line tools, and real-time applications with a single language.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Flutter/[0]-Introduction-to-Flutter.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/flutter/flutter-original.svg" width="40" title="Flutter"/><br>Flutter</a></td>
<td align="left" width="75%">Google's open-source UI framework that uses the Dart language to build natively compiled mobile, web, and desktop applications from a single codebase.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Angular/[0]-Introduction-to-Angular.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/angular/angular-original.svg" width="40" title="Angular"/><br>Angular</a></td>
<td align="left" width="75%">A full-featured, TypeScript-based frontend framework maintained by Google, offering built-in routing, forms, and dependency injection for large-scale applications.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Vue.js/[0]-Introduction-to-Vue.js.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/vuejs/vuejs-original.svg" width="40" title="Vue.js"/><br>Vue.js</a></td>
<td align="left" width="75%">A progressive JavaScript framework known for its gentle learning curve and flexibility, suitable for anything from small widgets to full single-page applications.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Next.js/[0]-Introduction-to-Next.js.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nextjs/nextjs-original.svg" width="40" title="Next.js"/><br>Next.js</a></td>
<td align="left" width="75%">A React-based full-stack framework that adds file-based routing, server-side rendering, and API routes, commonly used for SEO-friendly and production-grade web apps.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Express/[0]-Introduction-to-Express.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/express/express-original.svg" width="40" title="Express"/><br>Express</a></td>
<td align="left" width="75%">A minimal and flexible Node.js web framework for building REST APIs and web servers, and the foundation for much of the Node.js ecosystem.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./NextJS/[0]-Introduction-to-NextJS.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nestjs/nestjs-original.svg" width="40" title="NestJS"/><br>NestJS</a></td>
<td align="left" width="75%">An opinionated, TypeScript-first Node.js framework with a modular architecture inspired by Angular, designed for scalable and maintainable server-side applications.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Svelte/[0]-Introduction-to-Svelte.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/svelte/svelte-original.svg" width="40" title="Svelte"/><br>Svelte</a></td>
<td align="left" width="75%">A frontend framework that compiles components into small, fast vanilla JavaScript at build time, resulting in less code and no heavy runtime in the browser.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./TailwindCSS/[0]-Introduction-to-TailwindCSS.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/tailwindcss/tailwindcss-original.svg" width="40" title="Tailwind CSS"/><br>Tailwind CSS</a></td>
<td align="left" width="75%">A utility-first CSS framework that provides small, single-purpose classes you combine directly in your markup to build custom designs quickly, and pairs well with React, Next.js, Vue, and Svelte.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./ReactNative/[0]-Introduction-to-ReactNative.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/react/react-original.svg" width="40" title="React Native"/><br>React Native</a></td>
<td align="left" width="75%">A framework for building native iOS and Android apps using React and JavaScript, letting web developers reuse their skills on mobile.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Django/[0]-Introduction-to-Django.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/django/django-plain.svg" width="40" title="Django"/><br>Django</a></td>
<td align="left" width="75%">A "batteries-included" Python web framework with a built-in ORM, admin panel, and authentication, designed for rapid development of secure, maintainable websites.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Flask/[0]-Introduction-to-Flask.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/flask/flask-original.svg" width="40" title="Flask"/><br>Flask</a></td>
<td align="left" width="75%">A lightweight Python micro-framework that gives you the essentials for web apps and APIs and lets you add only the extensions you need.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./FastAPI/[0]-Introduction-to-FastAPI.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/fastapi/fastapi-original.svg" width="40" title="FastAPI"/><br>FastAPI</a></td>
<td align="left" width="75%">A modern, high-performance Python framework for building APIs, using type hints to provide automatic validation and interactive documentation.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./SpringBoot/[0]-Introduction-to-SpringBoot.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/spring/spring-original.svg" width="40" title="Spring Boot"/><br>Spring Boot</a></td>
<td align="left" width="75%">A Java framework that simplifies building production-ready, stand-alone applications and microservices, widely used in enterprise environments.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Laravel/[0]-Introduction-to-Laravel.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/laravel/laravel-original.svg" width="40" title="Laravel"/><br>Laravel</a></td>
<td align="left" width="75%">An expressive PHP framework with elegant syntax, built-in tooling for routing, queues, and authentication, and a large ecosystem for modern web applications.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./RubyonRails/[0]-Introduction-to-RubyonRails.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/rails/rails-original-wordmark.svg" width="40" title="Ruby on Rails"/><br>Ruby on Rails</a></td>
<td align="left" width="75%">A convention-over-configuration Ruby framework that favors rapid development, known for powering many startups and full-stack web products.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./ASP.NETCore/[0]-Introduction-to-ASP.NETCore.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/dotnetcore/dotnetcore-original.svg" width="40" title="ASP.NET Core"/><br>ASP.NET Core</a></td>
<td align="left" width="75%">Microsoft's cross-platform, high-performance framework for building web apps, APIs, and cloud services with C#.</td>
</tr>

<tr>
<td align="center" width="25%" height="100"><a href="./Electron/[0]-Introduction-to-Electron.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/electron/electron-original.svg" width="40" title="Electron"/><br>Electron</a></td>
<td align="left" width="75%">An open-source framework for building cross-platform desktop apps with JavaScript, HTML, and CSS, powered by Chromium and Node.js.</td>
</tr>

</table>

---

### Table of Contents

**Basics**
   1. **[What Are Frameworks?](./[1]-What-Are-Frameworks.md)**  
       1.1 Defining a Framework  
       1.2 Frameworks vs Libraries vs Runtimes  
       1.3 Why Frameworks Exist  
       1.4 Key Benefits and Trade-offs  
   2. **[Types Of Frameworks](./[2]-Types-Of-Frameworks.md)**  
       2.1 Frontend Frameworks  
       2.2 Backend Frameworks  
       2.3 Full-Stack and Meta-Frameworks  
       2.4 Mobile and Cross-Platform Frameworks  
   3. **[The Framework Ecosystem](./[3]-The-Framework-Ecosystem.md)**  
       3.1 Languages and Their Frameworks  
       3.2 Runtimes and Package Managers  
       3.3 Inversion of Control ("The Framework Calls You")  
       3.4 Popular Framework Examples  

**Application Structure**

   4. **[Project Structure And Conventions](./[4]-Project-Structure-And-Conventions.md)**  
       4.1 Anatomy of a Framework Project  
       4.2 Configuration Files  
       4.3 Convention over Configuration  
       4.4 A Simple Example Project  
   5. **[Dependencies And Package Management](./[5]-Dependencies-And-Package-Management.md)**  
       5.1 What Is a Dependency  
       5.2 Package Managers (npm, pip, Composer, and more)  
       5.3 Versioning and Lock Files  
       5.4 Keeping Dependencies Safe and Updated  
   6. **[Architecture Patterns](./[6]-Architecture-Patterns.md)**  
       6.1 MVC (Model-View-Controller)  
       6.2 Component-Based Architecture  
       6.3 Layered and Modular Architecture  
       6.4 Choosing a Pattern  
   7. **[Development Workflow](./[7]-Development-Workflow.md)**  
       7.1 Dev Servers and Hot Reloading  
       7.2 CLIs and Scaffolding Tools  
       7.3 Debugging Basics  
       7.4 Environment Variables and Configuration  

**Building The Frontend**

   8. **[Components And Rendering](./[8]-Components-And-Rendering.md)**  
       8.1 What Is a Component  
       8.2 Props and Composition  
       8.3 Templates, JSX, and Widgets  
       8.4 Virtual DOM vs Compiled Rendering  
   9. **[State Management](./[9]-State-Management.md)**  
       9.1 What Is State  
       9.2 Local vs Global State  
       9.3 Reactivity and Data Flow  
       9.4 State Management Approaches  
   10. **[Routing And Navigation](./[10]-Routing-And-Navigation.md)**  
       10.1 Client-Side vs Server-Side Routing  
       10.2 Defining Routes  
       10.3 Dynamic and Nested Routes  
       10.4 Navigation in Mobile Apps  
   11. **[Styling And User Interfaces](./[11]-Styling-And-User-Interfaces.md)**  
       11.1 CSS and Component Styling  
       11.2 UI Component Libraries  
       11.3 Responsive and Adaptive Design  
       11.4 Accessibility Basics  

**Building The Backend**

   12. **[HTTP And REST APIs](./[12]-HTTP-And-REST-APIs.md)**  
       12.1 How the Web Request Cycle Works  
       12.2 HTTP Methods and Status Codes  
       12.3 Designing REST Endpoints  
       12.4 JSON and Data Formats  
   13. **[Middleware And Request Handling](./[13]-Middleware-And-Request-Handling.md)**  
       13.1 What Is Middleware  
       13.2 Request and Response Lifecycle  
       13.3 Validation and Error Handling  
       13.4 Logging and Common Middleware  
   14. **[Working With Databases](./[14]-Working-With-Databases.md)**  
       14.1 Connecting a Framework to a Database  
       14.2 ORMs and Query Builders  
       14.3 Migrations  
       14.4 Models and Data Validation  
   15. **[Authentication And Security](./[15]-Authentication-And-Security.md)**  
       15.1 Authentication vs Authorization  
       15.2 Sessions, Cookies, and Tokens (JWT)  
       15.3 Common Vulnerabilities (XSS, CSRF, Injection)  
       15.4 Security Best Practices  

**Quality And Delivery**

   16. **[Testing](./[16]-Testing.md)**  
       16.1 Why Test  
       16.2 Unit, Integration, and End-to-End Tests  
       16.3 Testing Tools and Frameworks  
       16.4 Writing Your First Test  
   17. **[Building And Deployment](./[17]-Building-And-Deployment.md)**  
       17.1 Build Tools and Bundlers  
       17.2 Environments (Development, Staging, Production)  
       17.3 Hosting and Deployment Options  
       17.4 CI/CD Basics  
   18. **[Performance And Scaling](./[18]-Performance-And-Scaling.md)**  
       18.1 Rendering Strategies (CSR, SSR, SSG)  
       18.2 Caching and Lazy Loading  
       18.3 Optimizing Bundle Size and Load Time  
       18.4 Scaling a Framework-Based App  

**Next Steps**

   19. **[Choosing A Framework](./[19]-Choosing-A-Framework.md)**  
       19.1 Matching Frameworks to Projects  
       19.2 Frontend, Backend, and Mobile Considerations  
       19.3 Community, Ecosystem, and Long-Term Support  
       19.4 Where to Go Next