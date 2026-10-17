[Previous](./[3]-The-Middleware-Pipeline.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[5]-Minimal-APIs.md)

*Basics*

# Lesson 4 - Routing And Endpoints

## 4.1 Endpoint routing

**Routing** maps an incoming request (method + URL) to the code that should handle it. That code is called an **endpoint**. An endpoint is an object that contains:

- a **route pattern** (e.g. `/products/{id}`),
- a **request delegate** (the handler to run),
- **metadata** (HTTP methods, authorization policy, CORS, names, etc.).

**Two-step process.** Routing is split in two so that other middleware can inspect the selected endpoint:

```
Request
  ├─ 1. Routing middleware  → matches the URL, SELECTS an endpoint (nothing runs yet)
  ├─ 2. Middleware between  → Authorization / CORS read the endpoint's metadata
  └─ 3. Endpoint middleware → EXECUTES the selected endpoint
```

This is why `[Authorize]` can be enforced before your action runs: the authorization middleware looks at the chosen endpoint's metadata.

Every kind of ASP.NET Core app registers endpoints:

```csharp
app.MapGet("/hello", () => "Hello");              // minimal API
app.MapControllers();                              // MVC / API controllers
app.MapRazorPages();                               // Razor Pages
app.MapHub<ChatHub>("/chat");                      // SignalR
app.MapHealthChecks("/health");                    // health checks
```

**Matching rules:**

- If **no** endpoint matches the URL, the response is **404 Not Found**.
- If the URL matches but the **HTTP method** does not, the response is **405 Method Not Allowed**.
- If several routes match, the **most specific** one wins (literal segments beat parameters; constrained parameters beat unconstrained). If two are equally specific, an `AmbiguousMatchException` is thrown.
- Route matching on literal text is **case-insensitive**.

```csharp
app.MapGet("/products/featured", () => "featured");   // literal: wins
app.MapGet("/products/{id}", (string id) => id);       // parameter
```

---

## 4.2 Route templates and constraints

A **route template** is a URL pattern with literal segments and `{parameters}`.

| Template | Matches | Notes |
|---|---|---|
| `/products` | `/products` | Literal only |
| `/products/{id}` | `/products/5` | `id` = `"5"` |
| `/products/{id?}` | `/products` and `/products/5` | `?` makes it optional |
| `/products/{id=1}` | `/products` | Default value `1` |
| `/files/{*path}` | `/files/a/b/c.txt` | Catch-all: `path` = `"a/b/c.txt"` |
| `/{category}/{name}` | `/books/dune` | Multiple parameters |

Parameters are bound to handler parameters by **name**:

```csharp
app.MapGet("/products/{id}", (int id) => $"Product {id}");
```

**Constraints** restrict what a parameter may match. Write them after a colon:

```csharp
app.MapGet("/products/{id:int}", (int id) => $"Product {id}");
app.MapGet("/users/{name:alpha:length(3,20)}", (string name) => name);
```

| Constraint | Meaning | Example |
|---|---|---|
| `int`, `long` | Integer | `{id:int}` |
| `bool` | `true`/`false` | `{active:bool}` |
| `guid` | A GUID | `{id:guid}` |
| `datetime` | A date/time | `{date:datetime}` |
| `alpha` | Letters only | `{name:alpha}` |
| `min(n)`, `max(n)` | Numeric bounds | `{age:min(18)}` |
| `range(a,b)` | Numeric range | `{page:range(1,100)}` |
| `length(n)` / `length(a,b)` | String length | `{code:length(5)}` |
| `minlength(n)`, `maxlength(n)` | String length bounds | `{name:minlength(2)}` |
| `regex(...)` | Regular expression (double the `{ }` and `\\` in C# strings) | `{zip:regex(^\\d{{5}}$)}` |

Constraints also **disambiguate** routes:

```csharp
app.MapGet("/items/{id:int}",  (int id)    => $"by id {id}");
app.MapGet("/items/{slug}",    (string slug) => $"by slug {slug}");
// /items/42  → first;  /items/blue-shirt → second
```

Important: constraints are for **routing**, not validation. A request that fails a constraint produces **404**, not a `400` with an error message. Validate business rules separately (Lesson 9).

**Restricting HTTP methods** for one endpoint:

```csharp
app.MapMethods("/ping", new[] { "GET", "HEAD" }, () => "pong");
```

---

## 4.3 Route groups

When many endpoints share a prefix or the same settings, use `MapGroup`. The group applies a common prefix and metadata to all its endpoints.

```csharp
var products = app.MapGroup("/api/products")
                  .WithTags("Products")
                  .RequireAuthorization();

products.MapGet("/",        () => Results.Ok(new[] { "A", "B" }));
products.MapGet("/{id:int}", (int id) => Results.Ok($"Product {id}"));
products.MapPost("/",       (ProductDto dto) => Results.Created($"/api/products/1", dto));
products.MapDelete("/{id:int}", (int id) => Results.NoContent())
        .RequireAuthorization("AdminOnly");   // adds to the group's settings

public record ProductDto(string Name, decimal Price);
```

What a group can configure for every endpoint inside it:

| Method | Effect |
|---|---|
| `WithTags("...")` | OpenAPI grouping |
| `RequireAuthorization(...)` | Authorization policy |
| `AllowAnonymous()` | Opt out of authorization |
| `RequireCors("policy")` | CORS policy |
| `RequireRateLimiting("policy")` | Rate limiting |
| `AddEndpointFilter(...)` | Shared filter (Lesson 5) |
| `WithOpenApi`/`Produces<T>` | Documentation metadata |

**Groups can nest**, and inner groups inherit from outer ones:

```csharp
var api = app.MapGroup("/api").RequireAuthorization();
var v1  = api.MapGroup("/v1");
var orders = v1.MapGroup("/orders");     // /api/v1/orders
orders.MapGet("/", () => "orders");
```

Groups are also how you split a large app across files (see 5.4).

---

## 4.4 Link generation

Hard-coding URLs is fragile. **Link generation** builds URLs from route information, so if a route template changes, every generated link updates automatically.

**Step 1: name the endpoint.**

```csharp
app.MapGet("/products/{id:int}", (int id) => $"Product {id}")
   .WithName("GetProduct");
```

**Step 2: generate a link** using `LinkGenerator` (inject it) or a result helper:

```csharp
app.MapGet("/links", (LinkGenerator links, HttpContext ctx) =>
{
    string? path = links.GetPathByName("GetProduct", new { id = 7 });
    // "/products/7"

    string? uri = links.GetUriByName(ctx, "GetProduct", new { id = 7 });
    // "https://localhost:5001/products/7"

    return Results.Ok(new { path, uri });
});
```

**Common use: the `Location` header after creating a resource (HTTP 201):**

```csharp
app.MapPost("/products", (ProductDto dto) =>
{
    int newId = 42;                                          // pretend we saved it
    return Results.CreatedAtRoute("GetProduct", new { id = newId }, dto);
});
```

**In MVC and Razor Pages** the same idea is exposed through `Url` helpers and tag helpers:

```csharp
// Controller
string? url = Url.Action("Details", "Products", new { id = 7 });   // "/Products/Details/7"
return RedirectToAction("Details", new { id = 7 });
```

```html
<!-- Razor view -->
<a asp-controller="Products" asp-action="Details" asp-route-id="7">View</a>
<!-- Razor Page -->
<a asp-page="/Products/Details" asp-route-id="7">View</a>
```

Extra values that are not part of the route template become **query string** parameters: `GetPathByName("GetProduct", new { id = 7, ref = "home" })` produces `/products/7?ref=home`.

---

[Previous](./[3]-The-Middleware-Pipeline.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[5]-Minimal-APIs.md)
