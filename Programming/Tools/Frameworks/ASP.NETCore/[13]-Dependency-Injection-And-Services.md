[Previous](./[12]-Migrations-And-Database-Management.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[14]-Configuration-And-Logging.md)

*Data And Services*

# Lesson 13 - Dependency Injection And Services

## 13.1 Service lifetimes

**Dependency Injection (DI)** is a design technique where a class receives the objects it depends on (its *dependencies*) from the outside instead of creating them itself. ASP.NET Core has a built-in **DI container** that creates and supplies those objects.

Without DI, a class is tightly coupled and hard to test:

```csharp
public class OrderService
{
    private readonly EmailSender _email = new EmailSender();   // hard-coded dependency
}
```

With DI, the dependency is declared in the constructor and supplied by the container:

```csharp
public class OrderService
{
    private readonly IEmailSender _email;
    public OrderService(IEmailSender email) => _email = email;   // injected
}
```

**Service lifetime** decides how long an instance lives and how often a new one is created:

| Lifetime | Registered with | New instance created | Typical use |
|---|---|---|---|
| **Transient** | `AddTransient` | Every time it is requested | Lightweight, stateless helpers |
| **Scoped** | `AddScoped` | Once per **scope** (once per HTTP request in web apps) | `DbContext`, unit-of-work, per-request state |
| **Singleton** | `AddSingleton` | Once for the **app's lifetime** | Caches, configuration, shared thread-safe services |

```csharp
builder.Services.AddTransient<IGuidGenerator, GuidGenerator>();
builder.Services.AddScoped<IOrderService, OrderService>();
builder.Services.AddSingleton<ISystemClock, SystemClock>();
```

**Demonstrating the difference.** If a class exposes `Guid Id { get; } = Guid.NewGuid()`, then within one request:

- a **transient** service injected twice has **two different** ids,
- a **scoped** service injected twice has the **same** id (but a different one on the next request),
- a **singleton** has the **same** id for every request.

**The captive dependency rule:** a service must not depend on a service with a **shorter** lifetime. For example, a **singleton** that takes a **scoped** `DbContext` would keep that one context forever (a "captive dependency"), causing stale data and thread-safety bugs.

```
Singleton  →  may depend on: Singleton
Scoped     →  may depend on: Scoped, Singleton
Transient  →  may depend on: anything
```

In the `Development` environment, ASP.NET Core **validates scopes** at startup and throws if you break this rule.

**Singletons must be thread-safe** because many requests use the same instance at once. Avoid mutable instance state in them, or protect it (locks, concurrent collections).

---

## 13.2 Registering services

You register services on `builder.Services` before `Build()`.

```csharp
// Interface + implementation (most common)
builder.Services.AddScoped<IProductService, ProductService>();

// Concrete class only
builder.Services.AddScoped<ReportGenerator>();

// A ready-made instance (singleton)
builder.Services.AddSingleton<IClock>(new SystemClock());

// A factory lambda, for custom construction logic
builder.Services.AddScoped<IPaymentGateway>(sp =>
{
    var config = sp.GetRequiredService<IConfiguration>();
    return new StripeGateway(config["Stripe:Key"]!);
});
```

**Consuming services.** Where the framework creates your class, it fills in constructor parameters automatically:

```csharp
// Controller / Razor Page / middleware / other service: constructor injection
public class ProductsController : ControllerBase
{
    private readonly IProductService _service;
    public ProductsController(IProductService service) => _service = service;
}

// Minimal API: parameters (inferred from the container)
app.MapGet("/products", (IProductService service) => service.GetAll());

// Razor views and Blazor components
@inject IProductService Service
```

**Constructor injection is the default.** Use it so dependencies are explicit and required.

**Multiple implementations of one interface:**

```csharp
builder.Services.AddScoped<INotifier, EmailNotifier>();
builder.Services.AddScoped<INotifier, SmsNotifier>();

public class AlertService
{
    public AlertService(IEnumerable<INotifier> notifiers) { ... }   // receives both
}
// Requesting a single INotifier returns the LAST one registered.
```

**`TryAdd`** registers a service only if none exists yet (handy in libraries):

```csharp
builder.Services.TryAddScoped<IProductService, ProductService>();   // Microsoft.Extensions.DependencyInjection.Extensions
```

**Open generics:**

```csharp
builder.Services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
// IRepository<Product> → Repository<Product>
```

**Grouping registrations** in an extension method keeps `Program.cs` tidy:

```csharp
public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddApplicationServices(this IServiceCollection services)
    {
        services.AddScoped<IProductService, ProductService>();
        services.AddScoped<IOrderService, OrderService>();
        services.AddSingleton<IClock, SystemClock>();
        return services;
    }
}

builder.Services.AddApplicationServices();
```

**Using scoped services outside a request** (for example in a background service, which is a singleton) requires creating a scope manually:

```csharp
public class CleanupWorker : BackgroundService
{
    private readonly IServiceScopeFactory _scopeFactory;
    public CleanupWorker(IServiceScopeFactory scopeFactory) => _scopeFactory = scopeFactory;

    protected override async Task ExecuteAsync(CancellationToken ct)
    {
        using var scope = _scopeFactory.CreateScope();
        var db = scope.ServiceProvider.GetRequiredService<AppDbContext>();
        // ... use db
    }
}
```

**Avoid the service locator anti-pattern:** injecting `IServiceProvider` and calling `GetService` all over your code hides dependencies. Use it only at composition boundaries like the example above.

---

## 13.3 Interfaces and abstractions

Registering `IService` → `ServiceImpl` instead of using concrete classes gives you:

- **Testability:** in tests, supply a fake implementation.
- **Replaceability:** switch an implementation (e.g. SMTP email → SendGrid) by changing one line.
- **Loose coupling:** consumers depend on a contract, not on how it works.

**Example: an email abstraction**

```csharp
public interface IEmailSender
{
    Task SendAsync(string to, string subject, string body);
}

public class SmtpEmailSender : IEmailSender
{
    public async Task SendAsync(string to, string subject, string body)
    {
        // send using SMTP ...
        await Task.CompletedTask;
    }
}

public class ConsoleEmailSender : IEmailSender          // for development
{
    public Task SendAsync(string to, string subject, string body)
    {
        Console.WriteLine($"EMAIL to {to}: {subject}");
        return Task.CompletedTask;
    }
}

public class WelcomeService
{
    private readonly IEmailSender _email;
    public WelcomeService(IEmailSender email) => _email = email;

    public Task WelcomeAsync(string address)
        => _email.SendAsync(address, "Welcome!", "Thanks for joining.");
}
```

**Choosing the implementation by environment:**

```csharp
if (builder.Environment.IsDevelopment())
    builder.Services.AddSingleton<IEmailSender, ConsoleEmailSender>();
else
    builder.Services.AddSingleton<IEmailSender, SmtpEmailSender>();

builder.Services.AddScoped<WelcomeService>();
```

**A unit test with a hand-written fake:**

```csharp
public class FakeEmailSender : IEmailSender
{
    public List<string> Sent { get; } = new();
    public Task SendAsync(string to, string subject, string body)
    {
        Sent.Add(to);
        return Task.CompletedTask;
    }
}

[Fact]
public async Task Welcome_sends_email()
{
    var fake = new FakeEmailSender();
    var service = new WelcomeService(fake);

    await service.WelcomeAsync("a@b.com");

    Assert.Contains("a@b.com", fake.Sent);
}
```

**Don't over-abstract.** Not every class needs an interface. Create one when you have (or expect) multiple implementations, need to fake it in tests, or want to hide a boundary such as an external system. Simple internal helpers often don't need one.

---

## 13.4 Options and keyed services

**Options pattern (preview).** Instead of injecting raw `IConfiguration`, bind a configuration section to a class and inject `IOptions<T>`. Lesson 14 covers it in detail; the short version:

```csharp
builder.Services.Configure<SmtpSettings>(builder.Configuration.GetSection("Smtp"));

public class SmtpEmailSender : IEmailSender
{
    private readonly SmtpSettings _settings;
    public SmtpEmailSender(IOptions<SmtpSettings> options) => _settings = options.Value;
    public Task SendAsync(string to, string subject, string body) => Task.CompletedTask;
}
```

**Keyed services** (.NET 8+) let you register several implementations of the same type under different **keys** and pick one by key:

```csharp
builder.Services.AddKeyedSingleton<ICache, MemoryCache>("local");
builder.Services.AddKeyedSingleton<ICache, RedisCache>("distributed");

// Constructor injection
public class ProductService
{
    private readonly ICache _cache;
    public ProductService([FromKeyedServices("distributed")] ICache cache) => _cache = cache;
}

// Minimal API parameter
app.MapGet("/cache-info", ([FromKeyedServices("local")] ICache cache) => cache.GetType().Name);

// Manual lookup
var local = serviceProvider.GetRequiredKeyedService<ICache>("local");
```

Keyed variants exist for each lifetime: `AddKeyedTransient`, `AddKeyedScoped`, `AddKeyedSingleton`.

**Other useful DI features:**

| Feature | Description |
|---|---|
| `GetRequiredService<T>()` | Returns the service or throws if it is not registered |
| `GetService<T>()` | Returns `null` if not registered |
| `IServiceScopeFactory` | Creates scopes manually (background work) |
| `IDisposable` handling | The container disposes services it creates when their scope ends |
| `ActivatorUtilities.CreateInstance<T>` | Create an object, filling constructor args from the container |

**Replacing built-in DI.** The built-in container is intentionally simple. For advanced needs (property injection, decorators, convention scanning), libraries like Autofac or Scrutor can be plugged in, but most apps never need them.

**Common DI errors:**

| Error message | Likely cause |
|---|---|
| *Unable to resolve service for type 'X'* | The service was never registered, or you registered a different type |
| *Cannot consume scoped service from singleton* | Captive dependency; change lifetimes or use `IServiceScopeFactory` |
| *A circular dependency was detected* | A depends on B and B depends on A; refactor |

---

[Previous](./[12]-Migrations-And-Database-Management.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[14]-Configuration-And-Logging.md)
