[Previous](./[17]-JWT-And-API-Security.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[19]-Testing-ASPNET-Core-Applications.md)

*Quality And Delivery*

# Lesson 18 - Web API Design

## 18.1 REST conventions

**REST** is a set of conventions for HTTP APIs. A well-designed API is predictable, so clients can guess how it works.

**Core ideas:**

- **Resources** are *nouns*, identified by URLs: `/products`, `/products/42`, `/orders/7/items`.
- **HTTP methods** are the *verbs*.
- **Representations** are usually JSON.
- Each request is **stateless**: it carries everything the server needs (e.g. the auth token).

| Method | Meaning | Example | Safe | Idempotent |
|---|---|---|---|---|
| `GET` | Read | `GET /products/42` | Yes | Yes |
| `POST` | Create (or run an action) | `POST /products` | No | No |
| `PUT` | Replace a whole resource | `PUT /products/42` | No | Yes |
| `PATCH` | Partially update | `PATCH /products/42` | No | Not guaranteed |
| `DELETE` | Remove | `DELETE /products/42` | No | Yes |

*Safe* = doesn't change data. *Idempotent* = repeating the request has the same effect as sending it once.

**URL design rules:**

- Use **plural nouns**, lowercase, hyphens: `/order-items`, not `/getOrderItems`.
- Express hierarchy with nesting, but keep it shallow: `/customers/5/orders`.
- Use the **query string** for filtering, sorting, paging: `/products?category=books&sort=-price&page=2&pageSize=20`.
- Don't put verbs in URLs except for genuine actions: `POST /orders/7/cancel`.

**Use the right status codes:**

| Code | When |
|---|---|
| **200 OK** | Successful GET/PUT/PATCH with a body |
| **201 Created** | POST created a resource; include a `Location` header |
| **204 No Content** | Success, nothing to return (DELETE, PUT) |
| **400 Bad Request** | Invalid input |
| **401 Unauthorized** | Not authenticated |
| **403 Forbidden** | Authenticated but not allowed |
| **404 Not Found** | Resource does not exist |
| **409 Conflict** | Conflicts with current state (duplicate, version mismatch) |
| **422 Unprocessable Content** | Valid syntax but failed business validation |
| **429 Too Many Requests** | Rate limited |
| **500 Internal Server Error** | Unexpected server fault |

**A complete resource example:**

```csharp
var products = app.MapGroup("/api/products");

products.MapGet("/", async (AppDbContext db, int page = 1, int pageSize = 20) =>
{
    pageSize = Math.Clamp(pageSize, 1, 100);                   // protect the server
    var items = await db.Products.AsNoTracking()
        .OrderBy(p => p.Id)
        .Skip((page - 1) * pageSize).Take(pageSize)
        .Select(p => new ProductDto(p.Id, p.Name, p.Price))
        .ToListAsync();
    return Results.Ok(items);
});

products.MapGet("/{id:int}", async (int id, AppDbContext db) =>
    await db.Products.FindAsync(id) is Product p
        ? Results.Ok(new ProductDto(p.Id, p.Name, p.Price))
        : Results.NotFound());

products.MapPost("/", async (CreateProductDto dto, AppDbContext db) =>
{
    var p = new Product { Name = dto.Name, Price = dto.Price };
    db.Products.Add(p);
    await db.SaveChangesAsync();
    return Results.Created($"/api/products/{p.Id}", new ProductDto(p.Id, p.Name, p.Price));
});

products.MapPut("/{id:int}", async (int id, UpdateProductDto dto, AppDbContext db) =>
{
    var p = await db.Products.FindAsync(id);
    if (p is null) return Results.NotFound();
    p.Name = dto.Name; p.Price = dto.Price;
    await db.SaveChangesAsync();
    return Results.NoContent();
});

products.MapDelete("/{id:int}", async (int id, AppDbContext db) =>
{
    var rows = await db.Products.Where(p => p.Id == id).ExecuteDeleteAsync();
    return rows == 0 ? Results.NotFound() : Results.NoContent();
});
```

**Consistent errors.** Return errors in the standard **Problem Details** (RFC 9457) format. Enable it globally:

```csharp
builder.Services.AddProblemDetails();
app.UseExceptionHandler();      // unhandled exceptions become problem+json
app.UseStatusCodePages();       // bare 404/405 etc. also become problem+json
```

```json
{ "type": "https://tools.ietf.org/html/rfc9110#section-15.5.5", "title": "Not Found", "status": 404 }
```

**Paging responses** commonly include metadata:

```json
{ "items": [ ... ], "page": 2, "pageSize": 20, "totalCount": 135 }
```

---

## 18.2 DTOs and mapping

A **DTO (Data Transfer Object)** is a class that defines exactly what crosses the API boundary. Don't expose your EF Core entities directly.

**Why use DTOs:**

| Reason | Explanation |
|---|---|
| **Security** | Avoid leaking fields like `PasswordHash`, and prevent over-posting (Lesson 9) |
| **Stability** | The database can change without breaking clients |
| **Shape** | Return only what clients need; flatten related data |
| **Serialization safety** | Entities with circular navigation properties break JSON serialization |
| **Different needs** | Create, update, and read models often differ (no `Id` on create) |

```csharp
// Entity (internal)
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public decimal Price { get; set; }
    public decimal Cost { get; set; }                  // internal only
    public Category Category { get; set; } = null!;
}

// DTOs (public contract): records are concise and immutable
public record ProductDto(int Id, string Name, decimal Price, string Category);

public record CreateProductDto(
    [property: Required, StringLength(100)] string Name,
    [property: Range(0.01, 100000)] decimal Price,
    int CategoryId);

public record UpdateProductDto(string Name, decimal Price);
```

**Mapping options:**

**1. Manual mapping with extension methods.** Explicit, fast, easy to debug, and no dependencies. Recommended for most apps:

```csharp
public static class ProductMappings
{
    public static ProductDto ToDto(this Product p)
        => new(p.Id, p.Name, p.Price, p.Category.Name);

    public static Product ToEntity(this CreateProductDto dto)
        => new() { Name = dto.Name, Price = dto.Price, CategoryId = dto.CategoryId };
}

var dtos = await db.Products.Include(p => p.Category).Select(p => p.ToDto()).ToListAsync();
// Note: for EF queries prefer an inline projection expression so SQL selects only those columns:
var better = await db.Products.Select(p => new ProductDto(p.Id, p.Name, p.Price, p.Category.Name)).ToListAsync();
```

**2. Source generators**, such as **Mapperly**, generate the mapping code at compile time (fast, no reflection):

```csharp
[Mapper]
public static partial class ProductMapper
{
    public static partial ProductDto ToDto(Product product);
}
```

**3. Runtime mapping libraries**, such as **AutoMapper**, map by convention using reflection. They reduce code but hide behavior, and can fail at runtime; check the library's current license terms before adopting.

**Guideline:** keep mapping out of controllers and endpoints; keep each DTO purpose-specific (`CreateX`, `UpdateX`, `XDto`); never reuse an entity as a request model.

---

## 18.3 Versioning

APIs change. **Versioning** lets you evolve an API without breaking existing clients. Only version for **breaking** changes (removing/renaming fields, changing behavior); adding optional fields is usually non-breaking.

**Common strategies:**

| Strategy | Example | Notes |
|---|---|---|
| **URL segment** | `/api/v1/products` | Most visible and simple; very common |
| **Query string** | `/api/products?api-version=1.0` | Easy to add; default in Asp.Versioning |
| **Header** | `X-Api-Version: 1.0` | Clean URLs; less discoverable |
| **Media type** | `Accept: application/json;v=1.0` | Purist; more complex |

The official library is **Asp.Versioning**:

```bash
dotnet add package Asp.Versioning.Mvc            # for controllers
dotnet add package Asp.Versioning.Http           # for minimal APIs
dotnet add package Asp.Versioning.Mvc.ApiExplorer   # OpenAPI integration
```

**Controllers with URL-segment versioning:**

```csharp
builder.Services.AddControllers();
builder.Services.AddApiVersioning(options =>
{
    options.DefaultApiVersion = new ApiVersion(1, 0);
    options.AssumeDefaultVersionWhenUnspecified = true;
    options.ReportApiVersions = true;                          // adds api-supported-versions header
    options.ApiVersionReader = new UrlSegmentApiVersionReader();
})
.AddMvc();

[ApiController]
[ApiVersion("1.0")]
[Route("api/v{version:apiVersion}/products")]
public class ProductsV1Controller : ControllerBase
{
    [HttpGet] public IActionResult Get() => Ok(new[] { new { Id = 1, Name = "Pen" } });
}

[ApiController]
[ApiVersion("2.0")]
[Route("api/v{version:apiVersion}/products")]
public class ProductsV2Controller : ControllerBase
{
    [HttpGet] public IActionResult Get() => Ok(new[] { new { Id = 1, Title = "Pen", Price = 1.5 } });
}
```

**Minimal APIs:**

```csharp
builder.Services.AddApiVersioning(options =>
{
    options.DefaultApiVersion = new ApiVersion(1, 0);
    options.AssumeDefaultVersionWhenUnspecified = true;
    options.ApiVersionReader = new UrlSegmentApiVersionReader();
});

var versionSet = app.NewApiVersionSet()
    .HasApiVersion(new ApiVersion(1, 0))
    .HasApiVersion(new ApiVersion(2, 0))
    .Build();

var v1 = app.MapGroup("/api/v{version:apiVersion}/products").WithApiVersionSet(versionSet);
v1.MapGet("/", () => "list").MapToApiVersion(1, 0);
v1.MapGet("/", () => "list with price").MapToApiVersion(2, 0);
```

(Exact package and method names can change between releases; consult the Asp.Versioning documentation for your version.)

**Good practices:** deprecate with notice (`[ApiVersion("1.0", Deprecated = true)]`), document each version, support at most a few versions at once, and share business logic in services so versions only differ at the edge (DTOs/mappings).

---

## 18.4 OpenAPI and Swagger

**OpenAPI** is a standard, machine-readable description of an HTTP API (endpoints, parameters, request/response shapes). Tools use it to generate **interactive documentation** (Swagger UI, Scalar), **client SDKs**, and tests. **Swagger** is the older name for the tooling around OpenAPI.

**Built-in support (.NET 9+).** The templates use `Microsoft.AspNetCore.OpenApi`:

```csharp
builder.Services.AddOpenApi();                  // generate the document

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();                           // serves /openapi/v1.json
}
```

Open `https://localhost:5001/openapi/v1.json` to see the generated JSON document.

**Add a UI.** The built-in package produces the document only. Add a viewer, for example **Scalar** or **Swagger UI**:

```bash
dotnet add package Scalar.AspNetCore
```

```csharp
using Scalar.AspNetCore;

if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
    app.MapScalarApiReference();               // UI at /scalar/v1
}
```

or Swagger UI (from the Swashbuckle package):

```bash
dotnet add package Swashbuckle.AspNetCore.SwaggerUI
```

```csharp
app.UseSwaggerUI(o => o.SwaggerEndpoint("/openapi/v1.json", "My API v1"));
```

**Swashbuckle.AspNetCore** (generator + UI in one package, popular on older versions): `builder.Services.AddEndpointsApiExplorer(); builder.Services.AddSwaggerGen();` and `app.UseSwagger(); app.UseSwaggerUI();`.

**Enrich the documentation with endpoint metadata:**

```csharp
app.MapGet("/api/products/{id:int}", async (int id, AppDbContext db) =>
        await db.Products.FindAsync(id) is Product p
            ? TypedResults.Ok(new ProductDto(p.Id, p.Name, p.Price, ""))
            : Results.NotFound())
   .WithName("GetProduct")
   .WithSummary("Gets a product by id")
   .WithDescription("Returns the product, or 404 if it does not exist.")
   .WithTags("Products")
   .Produces<ProductDto>(StatusCodes.Status200OK)
   .ProducesProblem(StatusCodes.Status404NotFound);
```

For controllers, use `[ProducesResponseType(typeof(ProductDto), 200)]` and enable XML comments (`<GenerateDocumentationFile>true</GenerateDocumentationFile>` in the `.csproj`) to include `/// <summary>` text in the docs.

**Describing JWT security** can be done with a document transformer (built-in generator) or `AddSecurityDefinition` (Swashbuckle) so the UI offers an "Authorize" button.

**Generating a client** from the OpenAPI document: tools like **Kiota**, **NSwag**, or **OpenAPI Generator** create typed clients (C#, TypeScript, ...) from the JSON.

**Guideline:** expose the UI in development and staging; decide deliberately whether production should serve it, since it reveals your API surface.

---

[Previous](./[17]-JWT-And-API-Security.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[19]-Testing-ASPNET-Core-Applications.md)
