[Previous](./[19]-Testing-ASPNET-Core-Applications.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[21]-Deployment.md)

*Quality And Delivery*

# Lesson 20 - Performance And Caching

## 20.1 Response and output caching

**Caching** stores the result of expensive work so it can be reused. It is often the biggest performance win available.

ASP.NET Core offers several caching layers:

| Layer | Where cached | Controlled by |
|---|---|---|
| **HTTP caching headers** | Browser / CDN / proxies | `Cache-Control` response header |
| **Response caching middleware** | Server | Honors HTTP cache headers |
| **Output caching middleware** (.NET 7+) | Server | **Your server-side policies**; more flexible |
| **In-memory / distributed / Hybrid cache** | Server | Your code (cache anything) |

**Cache-Control headers** tell clients how long they may reuse a response:

```csharp
app.MapGet("/api/countries", () => countries)
   .WithMetadata(new ResponseCacheAttribute { Duration = 3600, Location = ResponseCacheLocation.Any });

// Or set the header by hand
app.MapGet("/api/rates", (HttpContext ctx) =>
{
    ctx.Response.Headers.CacheControl = "public,max-age=60";
    return Results.Ok(rates);
});
```

In MVC, use `[ResponseCache(Duration = 60)]` on actions. Never mark user-specific or private data as `public`.

**Response caching middleware** (`AddResponseCaching` / `UseResponseCaching`) stores responses on the server according to those headers. It is limited (it respects client headers like `Cache-Control: no-cache`), so most new apps prefer **output caching**.

**Output caching** gives you control over what is cached, for how long, and how to invalidate it:

```csharp
builder.Services.AddOutputCache(options =>
{
    options.AddBasePolicy(b => b.Expire(TimeSpan.FromSeconds(10)));            // default for everything cached
    options.AddPolicy("Products", b => b.Expire(TimeSpan.FromMinutes(5))
                                        .Tag("products"));                     // tag for invalidation
});

var app = builder.Build();
app.UseCors();            // CORS before output caching
app.UseOutputCache();     // after UseRouting / UseCors, before endpoints

app.MapGet("/api/products", async (AppDbContext db) => await db.Products.ToListAsync())
   .CacheOutput("Products");

app.MapGet("/api/search", (string q) => $"results for {q}")
   .CacheOutput(p => p.SetVaryByQuery("q").Expire(TimeSpan.FromMinutes(1)));   // separate entry per q

// Invalidate by tag when data changes
app.MapPost("/api/products", async (Product p, AppDbContext db, IOutputCacheStore cache) =>
{
    db.Products.Add(p);
    await db.SaveChangesAsync();
    await cache.EvictByTagAsync("products", default);
    return Results.Created($"/api/products/{p.Id}", p);
});
```

For controllers use `[OutputCache(PolicyName = "Products")]`.

**Defaults to know:** only `GET`/`HEAD` with a `200` response are cached; requests carrying an `Authorization` header or that set cookies are not cached unless you override the policy.

**Application data caching** with `IMemoryCache` (per-server memory):

```csharp
builder.Services.AddMemoryCache();

public class CategoryService
{
    private readonly IMemoryCache _cache;
    private readonly AppDbContext _db;
    public CategoryService(IMemoryCache cache, AppDbContext db) { _cache = cache; _db = db; }

    public Task<List<Category>> GetAllAsync() =>
        _cache.GetOrCreateAsync("categories", async entry =>
        {
            entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(10);
            return await _db.Categories.AsNoTracking().ToListAsync();
        })!;
}
```

For several servers, use a **distributed cache** (`IDistributedCache` backed by Redis or SQL Server). **HybridCache** (.NET 9, `Microsoft.Extensions.Caching.Hybrid`) combines a local in-memory layer with a distributed one and prevents "cache stampedes" (many requests recomputing the same value at once):

```csharp
builder.Services.AddHybridCache();

public async Task<List<Category>> GetAsync(HybridCache cache, AppDbContext db, CancellationToken ct) =>
    await cache.GetOrCreateAsync("categories",
        async token => await db.Categories.AsNoTracking().ToListAsync(token),
        cancellationToken: ct);
```

Remember: caching trades **freshness** for speed. Decide how stale data may be and how it gets invalidated.

---

## 20.2 Async programming

A web server handles many requests with a limited pool of threads. If a thread **blocks** waiting for I/O (database, HTTP call, file), that thread is wasted. **`async`/`await`** frees the thread while waiting, so the same threads can serve more requests (better **scalability**, not necessarily faster single requests).

```csharp
// BLOCKING: ties up a thread for the whole database call
app.MapGet("/bad", (AppDbContext db) => db.Products.ToList());

// ASYNC: the thread is released while waiting
app.MapGet("/good", async (AppDbContext db) => await db.Products.ToListAsync());
```

**Rules:**

1. **Async all the way.** If a method awaits, its callers should be async too. Return `Task`/`Task<T>`.
2. **Never block on async code.** Avoid `.Result`, `.Wait()`, `.GetAwaiter().GetResult()`, and `Task.Run(...).Wait()`. Under load they cause **thread pool starvation**, where requests queue up and the app appears frozen.
3. **Don't wrap synchronous CPU work in `Task.Run` just to look async** inside a request. It doesn't help web throughput.
4. **Accept and pass a `CancellationToken`** so abandoned requests stop their work:

```csharp
app.MapGet("/products", async (AppDbContext db, CancellationToken ct) =>
    await db.Products.AsNoTracking().ToListAsync(ct));
```

In minimal APIs and controller actions, a `CancellationToken` parameter is automatically bound to `HttpContext.RequestAborted`.

5. **Run independent operations concurrently** with `Task.WhenAll`:

```csharp
var ordersTask  = ordersService.GetAsync(userId, ct);
var profileTask = profileService.GetAsync(userId, ct);
await Task.WhenAll(ordersTask, profileTask);
return new Dashboard(ordersTask.Result, profileTask.Result);   // safe: both already complete
```

(Only do this when the operations use **different** `DbContext` instances or non-DbContext resources. A single `DbContext` must not run two operations at the same time.)

6. **Avoid `async void`.** Use `async Task` so exceptions and completion can be observed (`async void` is only for event handlers).
7. Use `ValueTask` only for hot paths that often complete synchronously; otherwise `Task` is fine.
8. **Use `HttpClientFactory`** (`builder.Services.AddHttpClient()`) rather than creating new `HttpClient` objects per call, to avoid socket exhaustion and stale DNS.

**Other quick wins:** paginate large lists; return DTO projections; add database indexes; avoid N+1 queries (Lesson 11); use `AddDbContextPool` where appropriate; measure before optimizing (use logging, `dotnet-counters`, Application Insights, or BenchmarkDotNet).

---

## 20.3 Compression

**Response compression** shrinks text-based responses (JSON, HTML, CSS, JavaScript) before sending, reducing bandwidth and speeding up delivery on slow connections.

```csharp
builder.Services.AddResponseCompression(options =>
{
    options.EnableForHttps = true;                       // see the security note below
    options.Providers.Add<BrotliCompressionProvider>();
    options.Providers.Add<GzipCompressionProvider>();
    options.MimeTypes = ResponseCompressionDefaults.MimeTypes.Concat(new[] { "image/svg+xml" });
});

builder.Services.Configure<BrotliCompressionProviderOptions>(o => o.Level = CompressionLevel.Fastest);
builder.Services.Configure<GzipCompressionProviderOptions>(o => o.Level = CompressionLevel.Fastest);

var app = builder.Build();
app.UseResponseCompression();      // early in the pipeline, before middleware that writes responses
```

(`CompressionLevel` is in `System.IO.Compression`.)

**How it works:** the client advertises supported encodings (`Accept-Encoding: br, gzip`); the server picks one and replies with `Content-Encoding: br`. **Brotli** compresses better than **Gzip**; both are supported by all modern browsers.

**Security note:** compressing HTTPS responses that mix secrets with attacker-controlled text can enable **CRIME/BREACH**-style attacks. That's why `EnableForHttps` is **off by default**. If you enable it, don't compress responses that reflect user input alongside secrets (tokens), or use antiforgery/masking protections.

**Don't double-compress.** Reverse proxies (nginx, IIS, CDNs, Azure Front Door) often compress better and more cheaply; if they do, leave the middleware off. Static file compression can also be done at build time (pre-compressed `.br`/`.gz` assets).

Don't compress already-compressed formats (JPEG, PNG, MP4, ZIP): there is nothing to gain.

---

## 20.4 Health checks

**Health checks** are endpoints that report whether the app (and its dependencies) is working. Load balancers, Kubernetes, Docker, and monitoring tools poll them to decide whether to route traffic to an instance or restart it.

**Basic setup:**

```csharp
builder.Services.AddHealthChecks();

var app = builder.Build();
app.MapHealthChecks("/health");        // returns 200 "Healthy" (or 503 "Unhealthy")
```

**Check dependencies.** Add checks for your database and other services:

```bash
dotnet add package Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore
```

```csharp
builder.Services.AddHealthChecks()
    .AddDbContextCheck<AppDbContext>("database", tags: new[] { "ready" })
    .AddCheck("self", () => HealthCheckResult.Healthy(), tags: new[] { "live" })
    .AddCheck<ExternalApiHealthCheck>("payments-api", tags: new[] { "ready" });
```

**Custom check:**

```csharp
public class ExternalApiHealthCheck : IHealthCheck
{
    private readonly IHttpClientFactory _factory;
    public ExternalApiHealthCheck(IHttpClientFactory factory) => _factory = factory;

    public async Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context, CancellationToken ct = default)
    {
        try
        {
            var client = _factory.CreateClient();
            var response = await client.GetAsync("https://payments.example.com/ping", ct);
            return response.IsSuccessStatusCode
                ? HealthCheckResult.Healthy()
                : HealthCheckResult.Degraded($"Status {(int)response.StatusCode}");
        }
        catch (Exception ex)
        {
            return HealthCheckResult.Unhealthy("Payments API unreachable", ex);
        }
    }
}
```

The three result states are **Healthy**, **Degraded**, and **Unhealthy** (503).

**Liveness vs readiness endpoints** (the Kubernetes convention):

```csharp
app.MapHealthChecks("/health/live",  new HealthCheckOptions { Predicate = c => c.Tags.Contains("live") });
app.MapHealthChecks("/health/ready", new HealthCheckOptions { Predicate = c => c.Tags.Contains("ready") });
```

- **Liveness:** "Is the process running?" If it fails, restart the container. Keep it cheap and dependency-free.
- **Readiness:** "Can it serve traffic now?" (database reachable, migrations done). If it fails, stop sending traffic but don't necessarily restart.

**Security:** health endpoints can reveal infrastructure details. Don't expose detailed output publicly; protect them with `.RequireAuthorization()`, restrict by network, or return minimal text.

---

[Previous](./[19]-Testing-ASPNET-Core-Applications.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[21]-Deployment.md)
