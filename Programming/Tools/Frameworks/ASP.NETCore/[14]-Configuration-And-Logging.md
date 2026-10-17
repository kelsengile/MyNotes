[Previous](./[13]-Dependency-Injection-And-Services.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[15]-Authentication-And-Identity.md)

*Data And Services*

# Lesson 14 - Configuration And Logging

## 14.1 appsettings.json and environments

Configuration holds values that change between machines and environments (connection strings, feature flags, API URLs) so you don't hard-code them. Lesson 2 introduced the **sources** and their priority. This lesson shows how to use them well.

**`appsettings.json`** holds default values, shared by all environments:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "Default": "Server=(localdb)\\mssqllocaldb;Database=ShopDev;Trusted_Connection=True"
  },
  "Smtp": {
    "Host": "localhost",
    "Port": 25,
    "From": "noreply@example.com"
  }
}
```

**`appsettings.{Environment}.json`** overrides only what differs. With `ASPNETCORE_ENVIRONMENT=Production`, `appsettings.Production.json` is loaded after the base file:

```json
{
  "Logging": { "LogLevel": { "Default": "Warning" } },
  "Smtp": { "Host": "smtp.mycompany.com", "Port": 587 }
}
```

Merged result in Production: `Smtp:Host` and `Port` come from the Production file; `Smtp:From` remains from the base file.

**Setting the environment:**

```bash
# Linux/macOS
export ASPNETCORE_ENVIRONMENT=Staging
# Windows PowerShell
$env:ASPNETCORE_ENVIRONMENT = "Staging"
# One-off
dotnet run --environment Staging
```

Any name is allowed (`Staging`, `QA`, `Test`), and you can add a matching `appsettings.QA.json`.

**Overriding with environment variables** (very common in Docker and cloud hosts). Use `__` for nesting:

```bash
export ConnectionStrings__Default="Host=db;Database=shop;Username=app;Password=secret"
export Smtp__Port=2525
```

**Reading configuration directly:**

```csharp
string? host = builder.Configuration["Smtp:Host"];
int port = builder.Configuration.GetValue<int>("Smtp:Port", defaultValue: 25);
string? conn = builder.Configuration.GetConnectionString("Default");
```

**Hot reload.** JSON files are loaded with `reloadOnChange: true`, so editing `appsettings.json` while the app runs updates values (visible through `IOptionsMonitor`, see 14.2).

**Add custom sources** if needed:

```csharp
builder.Configuration.AddJsonFile("custom.json", optional: true, reloadOnChange: true);
builder.Configuration.AddEnvironmentVariables(prefix: "MYAPP_");
```

---

## 14.2 Options pattern

The **options pattern** binds a configuration section to a strongly typed class. You get IntelliSense, type safety (an `int` is an `int`), validation, and easy testing.

**1. Define the options class:**

```csharp
public class SmtpSettings
{
    public const string SectionName = "Smtp";

    [Required] public string Host { get; set; } = "";
    [Range(1, 65535)] public int Port { get; set; } = 25;
    [Required, EmailAddress] public string From { get; set; } = "";
}
```

**2. Register and bind it (with validation):**

```csharp
builder.Services.AddOptions<SmtpSettings>()
    .Bind(builder.Configuration.GetSection(SmtpSettings.SectionName))
    .ValidateDataAnnotations()
    .ValidateOnStart();                 // fail at startup if invalid, not on first use
```

(A shorter form is `builder.Services.Configure<SmtpSettings>(builder.Configuration.GetSection("Smtp"));`, without validation.)

**3. Inject it:**

```csharp
public class EmailService
{
    private readonly SmtpSettings _settings;

    public EmailService(IOptions<SmtpSettings> options)
    {
        _settings = options.Value;
    }
}
```

**Which interface to inject?**

| Interface | Lifetime | Sees config changes? | Use when |
|---|---|---|---|
| `IOptions<T>` | Singleton | No (read once) | Settings never change while running |
| `IOptionsSnapshot<T>` | Scoped | Yes, refreshed **per request** | Per-request freshness in web apps |
| `IOptionsMonitor<T>` | Singleton | Yes, live, with `OnChange` callback | Singletons or background services needing live updates |

```csharp
public class FeatureService
{
    private readonly IOptionsMonitor<FeatureSettings> _monitor;
    public FeatureService(IOptionsMonitor<FeatureSettings> monitor) => _monitor = monitor;

    public bool BetaEnabled => _monitor.CurrentValue.EnableBeta;   // always the latest value
}
```

**Direct binding** without DI, for very early startup needs:

```csharp
var smtp = builder.Configuration.GetSection("Smtp").Get<SmtpSettings>();
```

**Named options** allow several configurations of the same type (`services.Configure<MailSettings>("Marketing", ...)`), retrieved by `IOptionsSnapshot<T>.Get("Marketing")`.

---

## 14.3 ILogger and providers

**Logging** records what your app is doing, so you can diagnose problems. ASP.NET Core has a built-in logging abstraction: **`ILogger<T>`**, where `T` is the class doing the logging (it becomes the log's *category*).

```csharp
public class OrderService
{
    private readonly ILogger<OrderService> _logger;
    public OrderService(ILogger<OrderService> logger) => _logger = logger;

    public void Place(int orderId, string customer)
    {
        _logger.LogInformation("Placing order {OrderId} for {Customer}", orderId, customer);

        try
        {
            // ...
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Failed to place order {OrderId}", orderId);   // pass the exception first
            throw;
        }
    }
}
```

In minimal APIs you can inject `ILogger<Program>` or `ILogger<T>` directly. Before the app is built, use `app.Logger` after `Build()`.

**Log levels** (lowest to highest severity):

| Level | Method | Use for |
|---|---|---|
| Trace (0) | `LogTrace` | Very detailed diagnostics; may contain sensitive data |
| Debug (1) | `LogDebug` | Developer investigation |
| Information (2) | `LogInformation` | Normal flow (request handled, order placed) |
| Warning (3) | `LogWarning` | Unexpected but handled situations |
| Error (4) | `LogError` | Failures in the current operation |
| Critical (5) | `LogCritical` | App- or system-wide failure |
| None (6) | n/a | Disables logging |

**Message templates and structured logging.** Use **named placeholders**, not string interpolation:

```csharp
// GOOD: structured. OrderId and Customer are stored as separate searchable fields
_logger.LogInformation("Order {OrderId} placed by {Customer}", orderId, customer);

// BAD: builds a different string each time, loses structure
_logger.LogInformation($"Order {orderId} placed by {customer}");
```

Placeholders are filled **by position**, not by name. Structured logs can be filtered and queried in tools like Seq, Application Insights, or Elasticsearch.

**Configuring levels** in `appsettings.json` (`Default` and per-category):

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning",
      "Microsoft.EntityFrameworkCore.Database.Command": "Information",
      "MyApp.Services": "Debug"
    }
  }
}
```

**Providers** decide where logs go. Built-in providers: **Console**, **Debug** (IDE output), **EventSource**, **EventLog** (Windows). Control them in code:

```csharp
builder.Logging.ClearProviders();
builder.Logging.AddConsole();
builder.Logging.AddDebug();
```

Third-party providers are popular for production: **Serilog**, **NLog**, and cloud/telemetry sinks such as Application Insights or OpenTelemetry.

**High-performance logging** with the source generator avoids allocations and gives compile-time checking:

```csharp
public static partial class Log
{
    [LoggerMessage(EventId = 1001, Level = LogLevel.Information,
                   Message = "Order {OrderId} placed by {Customer}")]
    public static partial void OrderPlaced(this ILogger logger, int orderId, string customer);
}

_logger.OrderPlaced(42, "Alice");
```

**Do not log secrets or personal data** (passwords, tokens, full card numbers).

---

## 14.4 User secrets

Secrets (API keys, database passwords) must never be committed to source control. During development, **user secrets** store them outside your project folder, in your user profile.

**Set up:**

```bash
dotnet user-secrets init                                       # adds a UserSecretsId to the .csproj
dotnet user-secrets set "ConnectionStrings:Default" "Server=...;Password=devpass"
dotnet user-secrets set "Stripe:Key" "sk_test_123"
dotnet user-secrets list
dotnet user-secrets remove "Stripe:Key"
dotnet user-secrets clear
```

The `.csproj` gets:

```xml
<PropertyGroup>
  <UserSecretsId>a1b2c3d4-e5f6-7890-abcd-ef1234567890</UserSecretsId>
</PropertyGroup>
```

Secrets are stored in a JSON file (`secrets.json`) in:

- Windows: `%APPDATA%\Microsoft\UserSecrets\<UserSecretsId>\secrets.json`
- Linux/macOS: `~/.microsoft/usersecrets/<UserSecretsId>/secrets.json`

**Reading them is no different from any configuration:**

```csharp
var key = builder.Configuration["Stripe:Key"];   // or via the options pattern
```

They are loaded automatically **only in the `Development` environment**, after `appsettings*.json` and before environment variables, so they override JSON values.

**Important limits:**

- User secrets are **not encrypted**. They are only a way to keep secrets out of your repo.
- They are for **development only**. Do not use them in production.

**Secrets in production:**

| Option | Notes |
|---|---|
| **Environment variables** | Simple; set in the host, container, or CI/CD secret store |
| **Azure Key Vault** | `builder.Configuration.AddAzureKeyVault(...)` with managed identity |
| **AWS Secrets Manager / Google Secret Manager** | Cloud-native equivalents |
| **Kubernetes Secrets / Docker secrets** | Mounted as env vars or files |

**Rules of thumb:** keep placeholders (not real values) in `appsettings.json`; commit only non-secret defaults; rotate any secret that was ever committed by mistake; and use different credentials per environment.

---

[Previous](./[13]-Dependency-Injection-And-Services.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[15]-Authentication-And-Identity.md)
