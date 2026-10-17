[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to C#](/Programming/Syntax/C#/[0]-Introduction-to-C#.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/dotnetcore/dotnetcore-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">ASP.NET Core</h1>
</div>


ASP.NET Core is Microsoft's cross-platform, high-performance framework for building web apps, APIs, and cloud services with C#. It runs on Windows, Linux, and macOS, and ships with dependency injection, configuration, logging, and a flexible middleware pipeline built in.

It supports several styles of development, including minimal APIs, MVC, Razor Pages, and Blazor. This Topic covers the hosting model, middleware, routing, Entity Framework Core, Identity and authorization, API design, testing, and deployment to Docker and the cloud.

Download: [dotnet.microsoft.com/download](https://dotnet.microsoft.com/download)

### Why Study ASP.NET Core?

- **Excellent performance** — consistently ranks among the fastest mainstream web frameworks.
- **Strong enterprise adoption** — widely used in corporate, finance, and government systems.
- **Great tooling** — Visual Studio, Rider, and the .NET CLI offer first-class debugging and productivity.
- **One platform, many app types** — web APIs, server-rendered pages, and Blazor UIs share the same foundation.

### ASP.NET Core At A Glance

| Attribute | Details |
|---|---|
| Language | C# |
| Type | Backend / full-stack framework |
| Maintained by | Microsoft |
| Best for | Enterprise web apps, APIs, cloud services |
| Pairs well with | Entity Framework Core, SQL Server, Azure, Docker |

---

### Table of Contents

**Basics**

   1. **[What Is ASP.NET Core](./[1]-What-Is-ASPNET-Core.md)**  
       1.1 The .NET platform  
       1.2 Kestrel and cross-platform hosting  
       1.3 Installing the SDK and using the dotnet CLI  
       1.4 Tour of a project  
   2. **[Program And Hosting](./[2]-Program-And-Hosting.md)**  
       2.1 Program.cs and the builder  
       2.2 The host and web server  
       2.3 Service registration  
       2.4 Configuration sources  
   3. **[The Middleware Pipeline](./[3]-The-Middleware-Pipeline.md)**  
       3.1 What middleware is  
       3.2 Built-in middleware  
       3.3 Writing custom middleware  
       3.4 Pipeline order  
   4. **[Routing And Endpoints](./[4]-Routing-And-Endpoints.md)**  
       4.1 Endpoint routing  
       4.2 Route templates and constraints  
       4.3 Route groups  
       4.4 Link generation  
   5. **[Minimal APIs](./[5]-Minimal-APIs.md)**  
       5.1 Defining endpoints  
       5.2 Parameter binding  
       5.3 Results and filters  
       5.4 Organizing a minimal API  

**Server-Side Development**

   6. **[MVC Controllers](./[6]-MVC-Controllers.md)**  
       6.1 The MVC pattern  
       6.2 Controllers and actions  
       6.3 Action results  
       6.4 API controllers  
   7. **[Views And Razor Syntax](./[7]-Views-And-Razor-Syntax.md)**  
       7.1 Razor basics  
       7.2 Layouts and partials  
       7.3 Tag helpers  
       7.4 View components  
   8. **[Razor Pages](./[8]-Razor-Pages.md)**  
       8.1 Page model  
       8.2 Handlers  
       8.3 Routing in Razor Pages  
       8.4 When to choose Razor Pages  
   9. **[Model Binding And Validation](./[9]-Model-Binding-And-Validation.md)**  
       9.1 Binding sources  
       9.2 Data annotations  
       9.3 FluentValidation  
       9.4 Validation responses  
   10. **[Blazor Components](./[10]-Blazor-Components.md)**  
       10.1 Blazor hosting models  
       10.2 Components and parameters  
       10.3 Event handling and binding  
       10.4 Forms and interop  

**Data And Services**

   11. **[Entity Framework Core](./[11]-Entity-Framework-Core.md)**  
       11.1 DbContext and entities  
       11.2 LINQ queries  
       11.3 Relationships  
       11.4 Tracking and performance  
   12. **[Migrations And Database Management](./[12]-Migrations-And-Database-Management.md)**  
       12.1 Creating migrations  
       12.2 Applying and reverting  
       12.3 Seeding data  
       12.4 Working with SQL Server and PostgreSQL  
   13. **[Dependency Injection And Services](./[13]-Dependency-Injection-And-Services.md)**  
       13.1 Service lifetimes  
       13.2 Registering services  
       13.3 Interfaces and abstractions  
       13.4 Options and keyed services  
   14. **[Configuration And Logging](./[14]-Configuration-And-Logging.md)**  
       14.1 appsettings.json and environments  
       14.2 Options pattern  
       14.3 ILogger and providers  
       14.4 User secrets  

**Security**

   15. **[Authentication And Identity](./[15]-Authentication-And-Identity.md)**  
       15.1 ASP.NET Core Identity  
       15.2 Cookies and sessions  
       15.3 External logins  
       15.4 Password handling  
   16. **[Authorization](./[16]-Authorization.md)**  
       16.1 Roles  
       16.2 Claims  
       16.3 Policy-based authorization  
       16.4 Resource-based checks  
   17. **[JWT And API Security](./[17]-JWT-And-API-Security.md)**  
       17.1 JWT bearer authentication  
       17.2 CORS  
       17.3 Rate limiting  
       17.4 HTTPS and data protection  

**Quality And Delivery**

   18. **[Web API Design](./[18]-Web-API-Design.md)**  
       18.1 REST conventions  
       18.2 DTOs and mapping  
       18.3 Versioning  
       18.4 OpenAPI and Swagger  
   19. **[Testing ASP.NET Core Applications](./[19]-Testing-ASPNET-Core-Applications.md)**  
       19.1 xUnit basics  
       19.2 Unit tests  
       19.3 Integration tests with WebApplicationFactory  
       19.4 Mocking  
   20. **[Performance And Caching](./[20]-Performance-And-Caching.md)**  
       20.1 Response and output caching  
       20.2 Async programming  
       20.3 Compression  
       20.4 Health checks  
   21. **[Deployment](./[21]-Deployment.md)**  
       21.1 Publishing apps  
       21.2 Docker containers  
       21.3 Azure, IIS, and Linux hosting  
       21.4 CI/CD basics  
   22. **[Next Steps With ASP.NET Core](./[22]-Next-Steps-With-ASPNET-Core.md)**  
       22.1 gRPC and SignalR  
       22.2 Background services  
       22.3 Clean architecture  
       22.4 Project ideas
