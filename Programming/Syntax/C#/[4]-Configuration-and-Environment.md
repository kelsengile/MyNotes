[Previous](./[3]-Projects-and-Solutions.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[5]-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 4 - Configuration & Environment (appsettings.json, environment variables)

## 4.1 appsettings.json

Most .NET applications (especially ASP.NET Core apps) store settings in a JSON file called `appsettings.json`:

```json
{
  "ConnectionStrings": {
    "Default": "Server=localhost;Database=Shop;"
  },
  "Logging": {
    "LogLevel": { "Default": "Information" }
  }
}
```

This keeps configuration separate from code, so you can change settings without recompiling.

---

## 4.2 Environment Variables

Environment variables let you override settings per machine, without touching any file — useful for secrets like connection strings or API keys that shouldn't be committed to source control.

```bash
export ConnectionStrings__Default="Server=prod;Database=Shop;"
```

The double underscore (`__`) maps to the nested JSON path `ConnectionStrings:Default`.

---

## 4.3 Environments (Development, Staging, Production)

.NET recognizes an environment name (commonly `Development`, `Staging`, or `Production`) via the `ASPNETCORE_ENVIRONMENT` or `DOTNET_ENVIRONMENT` variable. This lets you keep environment-specific files like `appsettings.Development.json`, which are automatically layered on top of the base `appsettings.json` when that environment is active — so local development can use different settings than what runs in production.

[Previous](./[3]-Projects-and-Solutions.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[5]-Variables-and-Data-Types.md)
