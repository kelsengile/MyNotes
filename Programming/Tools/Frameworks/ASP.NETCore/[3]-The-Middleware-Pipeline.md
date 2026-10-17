[Previous](./[2]-Program-And-Hosting.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[4]-Routing-And-Endpoints.md)

*Basics*

# Lesson 3 - The Middleware Pipeline

## 3.1 What middleware is

**Middleware** is a component that handles an HTTP request on its way in and the response on its way out. Middleware components are chained into a **pipeline**. Each one can:

- do work **before** passing the request on,
- call the **next** middleware (or not),
- do work **after** the rest of the pipeline has finished.

```
Request ─▶ [Middleware A] ─▶ [Middleware B] ─▶ [Middleware C] ─▶ Endpoint
Response ◀─ [Middleware A] ◀─ [Middleware B] ◀─ [Middleware C] ◀─ Endpoint
```

A middleware is simply a function that receives the `HttpContext` and a `next` delegate. Three methods build the pipeline:

| Method | Behavior |
|---|---|
| `app.Use(...)` | Runs logic and (usually) calls `next` |
| `app.Run(...)` | **Terminal.** Runs logic and does *not* call `next` |
| `app.Map(...)` | **Branches** the pipeline when the path matches |

```csharp
var app = WebApplication.CreateBuilder(args).Build();

app.Use(async (context, next) =>
{
    Console.WriteLine($"--> {context.Request.Method} {context.Request.Path}");
    await next(context);                       // call the next middleware
    Console.WriteLine($"<-- {context.Response.StatusCode}");
});

app.Run(async context =>
{
    await context.Response.WriteAsync("Hello from the end of the pipeline");
});

app.Run();
```

Note the difference: `app.Run(...)` registers a terminal middleware, while the final `app.Run()` (no arguments) starts the host.

**Short-circuiting.** A middleware may decide not to call `next`, stopping the request early (for example, returning `401` for a missing API key):

```csharp
app.Use(async (context, next) =>
{
    if (!context.Request.Headers.ContainsKey("X-Api-Key"))
    {
        context.Response.StatusCode = StatusCodes.Status401Unauthorized;
        await context.Response.WriteAsync("API key required");
        return;                                // short-circuit: next is never called
    }
    await next(context);
});
```

**Important:** do not write to the response body and *then* call `next` in a way that lets a later component change the status code or headers. Once the response has started (the first bytes are sent), headers and status are locked.

---

## 3.2 Built-in middleware

ASP.NET Core ships many ready-made components. The most common:

| Middleware | Purpose |
|---|---|
| `UseExceptionHandler` | Catches unhandled exceptions and produces an error response |
| `UseDeveloperExceptionPage` | Detailed error page (development only; enabled automatically in Development for `WebApplication`) |
| `UseHsts` | Adds the `Strict-Transport-Security` header (production) |
| `UseHttpsRedirection` | Redirects HTTP requests to HTTPS |
| `UseStaticFiles` | Serves files from `wwwroot` |
| `UseRouting` | Matches the request to an endpoint |
| `UseCors` | Applies Cross-Origin Resource Sharing rules |
| `UseAuthentication` | Identifies *who* the user is |
| `UseAuthorization` | Decides *what* the user may do |
| `UseRateLimiter` | Limits request rates |
| `UseResponseCompression` | Compresses responses |
| `UseOutputCache` | Caches endpoint output |
| `UseSession` | Enables session state |
| `UseStatusCodePages` | Friendly pages for 4xx/5xx without a body |

A typical production-ready setup:

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddControllers();
builder.Services.AddAuthentication().AddCookie();
builder.Services.AddAuthorization();

var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();
app.Run();
```

**Routing note:** with `WebApplication`, `UseRouting` and the endpoint execution step are added automatically at the start and end of the pipeline. You only call `app.UseRouting()` explicitly when you need to control *where* routing happens relative to other middleware (for example, placing `UseCors` or `UseAuthorization` after it).

---

## 3.3 Writing custom middleware

There are three ways to write middleware, from quickest to most structured.

**1. Inline** (shown in 3.1): fine for small, one-off logic.

**2. A convention-based class.** The class needs a constructor taking `RequestDelegate next` and a method named `Invoke` or `InvokeAsync` taking `HttpContext`:

```csharp
public class RequestTimingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<RequestTimingMiddleware> _logger;

    public RequestTimingMiddleware(RequestDelegate next, ILogger<RequestTimingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var watch = System.Diagnostics.Stopwatch.StartNew();

        await _next(context);                       // run the rest of the pipeline

        watch.Stop();
        _logger.LogInformation("{Method} {Path} took {Elapsed} ms",
            context.Request.Method, context.Request.Path, watch.ElapsedMilliseconds);
    }
}
```

Add a small extension method so it reads like built-in middleware:

```csharp
public static class RequestTimingMiddlewareExtensions
{
    public static IApplicationBuilder UseRequestTiming(this IApplicationBuilder app)
        => app.UseMiddleware<RequestTimingMiddleware>();
}

// Program.cs
app.UseRequestTiming();
```

The constructor runs **once** (the middleware instance is effectively a singleton). To use **scoped** services (like a `DbContext`), take them as extra parameters of `InvokeAsync`, not in the constructor:

```csharp
public async Task InvokeAsync(HttpContext context, AppDbContext db) { ... }
```

**3. Factory-based (`IMiddleware`).** Implement `IMiddleware` to be created per request with full DI lifetimes. You must also register it as a service:

```csharp
public class AuditMiddleware : IMiddleware
{
    public async Task InvokeAsync(HttpContext context, RequestDelegate next)
    {
        // before
        await next(context);
        // after
    }
}

builder.Services.AddTransient<AuditMiddleware>();
app.UseMiddleware<AuditMiddleware>();
```

**Branching helpers:**

```csharp
// Map: separate branch for a path prefix (the branch does not rejoin)
app.Map("/admin", adminApp =>
{
    adminApp.Run(async ctx => await ctx.Response.WriteAsync("Admin area"));
});

// MapWhen / UseWhen: branch on any condition. UseWhen rejoins the main pipeline.
app.UseWhen(ctx => ctx.Request.Path.StartsWithSegments("/api"), apiApp =>
{
    apiApp.UseMiddleware<RequestTimingMiddleware>();
});
```

---

## 3.4 Pipeline order

**Order is everything.** Middleware runs in the order you add it for requests, and in reverse for responses. A wrong order causes bugs that look mysterious.

Recommended order of the common components:

```
1. ExceptionHandler / HSTS        ← first, so it can catch errors from everything after
2. HttpsRedirection
3. StaticFiles                    ← short-circuits for files, skipping the rest
4. Routing
5. CORS                           ← after routing, before auth
6. Authentication                 ← who are you?
7. Authorization                  ← are you allowed?
8. (Rate limiter, session, caching, custom middleware as needed)
9. Endpoints (MapGet, MapControllers, MapRazorPages ...)
```

**Why the order matters (examples):**

- `UseAuthentication()` must come **before** `UseAuthorization()`. Authorization needs a populated user.
- `UseExceptionHandler()` must be **first**. It can only catch exceptions thrown by components registered *after* it.
- `UseStaticFiles()` before `UseAuthorization()` means static files are public. Put it after to protect them.
- `UseCors()` must be after `UseRouting()` and before `UseAuthorization()` for CORS to apply correctly to endpoints.

**Debugging order problems:** if a feature silently does nothing, check (1) that you called both the `AddXxx` registration and the `UseXxx` activation, and (2) that the `UseXxx` call is in the right position.

```csharp
// BUG: authorization runs without a user, so protected endpoints always fail
app.UseAuthorization();
app.UseAuthentication();

// CORRECT
app.UseAuthentication();
app.UseAuthorization();
```

---

[Previous](./[2]-Program-And-Hosting.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[4]-Routing-And-Endpoints.md)
