[Previous](./[1]-What-Is-ASPNET-Core.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[3]-The-Middleware-Pipeline.md)

*Basics*

# Lesson 2 - Program And Hosting

## 2.1 Program.cs and the builder

Every ASP.NET Core app is started from `Program.cs`. It has two distinct phases:

1. **Build phase:** use the `WebApplicationBuilder` to register services and settings.
2. **Run phase:** use the built `WebApplication` to define the request pipeline and endpoints, then start listening.

```csharp
// ---- Phase 1: Build ----
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();            // register services
var appName = builder.Configuration["AppName"]; // read configuration

var app = builder.Build();                     // freeze registrations

// ---- Phase 2: Run ----
app.UseHttpsRedirection();                     // middleware
app.MapGet("/", () => "Hello");               // endpoints

app.Run();                                     // start the server (blocks)
```

**What `WebApplication.CreateBuilder(args)` sets up for you** (with sensible defaults):

- **Kestrel** as the web server.
- **Configuration** loaded from `appsettings.json`, environment variables, command-line `args`, and more (see 2.4).
- **Logging** to the console and debug output.
- **Dependency injection** container.
- The **content root** (where your app's files are) and **web root** (`wwwroot`).

**Useful properties of the builder:**

| Property | Purpose |
|---|---|
| `builder.Services` | Register services in the DI container |
| `builder.Configuration` | Read or add configuration |
| `builder.Environment` | Environment name (`Development`, `Production`) and paths |
| `builder.Logging` | Configure logging providers |
| `builder.WebHost` | Configure the web server (e.g. Kestrel) |

**Important rule:** once you call `builder.Build()`, you can no longer register services. Everything in `builder.Services` must come **before** `Build()`.

`WebApplication` (the `app` object) implements three things at once: `IHost` (lifetime), `IApplicationBuilder` (middleware via `app.Use...`), and `IEndpointRouteBuilder` (endpoints via `app.Map...`).

---

## 2.2 The host and web server

Behind `WebApplication` is the **Generic Host** (`IHost`). The host owns the app's lifetime and bundles shared infrastructure: DI, configuration, logging, and background services. The web server (Kestrel) is just one component the host starts.

```
Host
 ├── Dependency Injection container
 ├── Configuration
 ├── Logging
 ├── IHostedService / BackgroundService instances
 └── Web server (Kestrel) ──▶ middleware pipeline ──▶ endpoints
```

`app.Run()` starts the host and blocks until the app is shut down (`Ctrl+C`, `SIGTERM`, or `app.StopAsync()`). The host performs a **graceful shutdown**: it stops accepting new requests and lets in-flight requests finish (default timeout is 30 seconds).

You can also start without blocking:

```csharp
await app.RunAsync();   // async version of Run()
```

**Choosing URLs.** Several ways exist, listed here from lowest to highest priority in typical setups:

```bash
# 1. launchSettings.json (development only)

# 2. Environment variable
export ASPNETCORE_URLS="http://localhost:6000;http://localhost:6001"

# 3. Command-line argument
dotnet run --urls "http://localhost:7000"
```

or in code:

```csharp
app.Urls.Add("http://localhost:8080");
// or
builder.WebHost.UseUrls("http://localhost:8080");
```

**Configuring Kestrel** (limits, certificates, protocols):

```csharp
builder.WebHost.ConfigureKestrel(options =>
{
    options.Limits.MaxRequestBodySize = 10 * 1024 * 1024;   // 10 MB
    options.ListenAnyIP(8080);                               // HTTP on port 8080
});
```

Kestrel endpoints can also be defined in `appsettings.json` under a `Kestrel:Endpoints` section, which is convenient for deployment.

**Hosted services** run alongside the web server (details in Lesson 22):

```csharp
builder.Services.AddHostedService<MyBackgroundWorker>();
```

---

## 2.3 Service registration

A **service** is any object your app uses (a database context, an email sender, a clock). Services are registered with the **built-in dependency injection (DI) container** so the framework can create them and pass them to the classes that need them.

Registration happens on `builder.Services`, before `Build()`:

```csharp
builder.Services.AddControllers();                      // framework feature
builder.Services.AddDbContext<AppDbContext>();          // EF Core
builder.Services.AddScoped<IOrderService, OrderService>(); // your own service
```

**Naming convention:** framework features expose an `AddXxx` extension method on `IServiceCollection` that registers everything the feature needs. Then a matching `UseXxx` or `MapXxx` call on `app` activates it in the pipeline:

| Register (build phase) | Activate (run phase) |
|---|---|
| `AddAuthentication()` | `UseAuthentication()` |
| `AddAuthorization()` | `UseAuthorization()` |
| `AddCors()` | `UseCors()` |
| `AddControllers()` | `MapControllers()` |
| `AddRazorPages()` | `MapRazorPages()` |
| `AddHealthChecks()` | `MapHealthChecks()` |

A service is later *consumed* by asking for it in a constructor or endpoint parameter:

```csharp
public class OrderService : IOrderService
{
    private readonly ILogger<OrderService> _logger;

    public OrderService(ILogger<OrderService> logger)   // injected automatically
    {
        _logger = logger;
    }
}

app.MapGet("/orders", (IOrderService orders) => orders.GetAll());
```

Lesson 13 covers lifetimes (Transient, Scoped, Singleton) and advanced registration in depth. For now, remember: **register in the build phase, receive via constructor or parameter.**

---

## 2.4 Configuration sources

ASP.NET Core builds a single, layered **configuration** from many sources. Each source is a set of key/value pairs. When the same key appears in several sources, **the last one wins**.

Default order (earlier = lower priority, later = higher priority):

1. `appsettings.json`
2. `appsettings.{Environment}.json` (e.g. `appsettings.Development.json`)
3. **User secrets** (only in the `Development` environment)
4. **Environment variables**
5. **Command-line arguments**

```json
// appsettings.json
{
  "AppName": "Shop",
  "ConnectionStrings": {
    "Default": "Server=.;Database=Shop;Trusted_Connection=True;TrustServerCertificate=True"
  },
  "Features": {
    "EnableBeta": false
  }
}
```

**Hierarchical keys** use a colon `:` in code and in JSON paths. In environment variables, use a double underscore `__` (because `:` is not valid in all shells):

```bash
export Features__EnableBeta=true
dotnet run --Features:EnableBeta=true
```

**Reading values:**

```csharp
var name = builder.Configuration["AppName"];                          // string?
var beta = builder.Configuration.GetValue<bool>("Features:EnableBeta"); // typed
var conn = builder.Configuration.GetConnectionString("Default");       // shortcut for ConnectionStrings:Default
var section = builder.Configuration.GetSection("Features");            // a sub-tree
```

Inject `IConfiguration` anywhere you need it:

```csharp
app.MapGet("/name", (IConfiguration config) => config["AppName"]);
```

(Lesson 14 shows the better **options pattern** for strongly typed settings.)

**Environments.** The environment name comes from the `ASPNETCORE_ENVIRONMENT` (or `DOTNET_ENVIRONMENT`) variable. Common values: `Development`, `Staging`, `Production`. If unset, it defaults to `Production`.

```csharp
if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();   // detailed errors, only for development
}
else
{
    app.UseExceptionHandler("/error"); // friendly errors in production
    app.UseHsts();
}
```

**Never put secrets (passwords, API keys) in `appsettings.json`** or commit them to source control. Use user secrets locally and environment variables or a secret store in production (Lesson 14).

---

[Previous](./[1]-What-Is-ASPNET-Core.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[3]-The-Middleware-Pipeline.md)
