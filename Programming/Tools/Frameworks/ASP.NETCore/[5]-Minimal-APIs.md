[Previous](./[4]-Routing-And-Endpoints.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[6]-MVC-Controllers.md)

*Basics*

# Lesson 5 - Minimal APIs

## 5.1 Defining endpoints

**Minimal APIs** let you build HTTP APIs with very little code: no controllers, no attributes required. You map an HTTP method + route to a handler (a lambda, local function, or method).

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/hello", () => "Hello");
app.MapPost("/echo", (Message m) => m);
app.MapPut("/items/{id}", (int id, Item item) => Results.NoContent());
app.MapDelete("/items/{id}", (int id) => Results.NoContent());
app.MapPatch("/items/{id}", (int id) => Results.Ok());

app.Run();

public record Message(string Text);
public record Item(string Name);
```

**What the handler returns becomes the response:**

| Return type | Response |
|---|---|
| `string` | `text/plain` body |
| An object / record / collection | Serialized as **JSON** (200 OK) |
| `IResult` (via `Results.*`) | Exactly the result you build |
| `Task<T>` / `async` | The awaited value, handled as above |
| `void` / `Task` | Empty 200 response |

**A small in-memory CRUD API:**

```csharp
var app = WebApplication.CreateBuilder(args).Build();

var todos = new List<Todo>();
var nextId = 1;

app.MapGet("/todos", () => todos);

app.MapGet("/todos/{id:int}", (int id) =>
    todos.FirstOrDefault(t => t.Id == id) is Todo todo
        ? Results.Ok(todo)
        : Results.NotFound());

app.MapPost("/todos", (NewTodo input) =>
{
    var todo = new Todo(nextId++, input.Title, false);
    todos.Add(todo);
    return Results.Created($"/todos/{todo.Id}", todo);
});

app.MapDelete("/todos/{id:int}", (int id) =>
{
    var removed = todos.RemoveAll(t => t.Id == id);
    return removed > 0 ? Results.NoContent() : Results.NotFound();
});

app.Run();

public record Todo(int Id, string Title, bool Done);
public record NewTodo(string Title);
```

Handlers can also be **named methods** or **static methods**, which improves readability and testability:

```csharp
app.MapGet("/todos", TodoHandlers.GetAll);

public static class TodoHandlers
{
    public static IResult GetAll() => Results.Ok(new[] { "a", "b" });
}
```

(This example keeps data in memory so you can focus on the API. Real apps store data with EF Core, Lesson 11.)

---

## 5.2 Parameter binding

Minimal APIs **infer** where each handler parameter comes from:

| Source | When it is inferred | Explicit attribute |
|---|---|---|
| **Route** | Name matches a `{placeholder}` in the route | `[FromRoute]` |
| **Query string** | Simple type not in the route (GET/DELETE) | `[FromQuery]` |
| **Header** | Never inferred | `[FromHeader(Name = "X-Key")]` |
| **Body (JSON)** | Complex type, on POST/PUT/PATCH | `[FromBody]` |
| **Form** | Never inferred for classes; `IFormFile`/`IFormCollection` are | `[FromForm]` |
| **Service (DI)** | Type is registered in DI | `[FromServices]` |
| **Special types** | `HttpContext`, `HttpRequest`, `HttpResponse`, `ClaimsPrincipal`, `CancellationToken` | n/a |

```csharp
app.MapGet("/search/{category}",
    (string category,                         // route
     string? q,                               // query ?q=...  (nullable = optional)
     int page,                                // query ?page=...  (required, no default!)
     [FromHeader(Name = "X-Client")] string? client,   // header
     ILogger<Program> logger,                 // DI service
     CancellationToken ct)                    // request-aborted token
        => Results.Ok(new { category, q, page, client }));
```

**Required vs optional:**

- A non-nullable parameter without a default is **required**. If it is missing, the framework returns **400 Bad Request** automatically.
- A nullable type (`string?`, `int?`) or a parameter with a default value (`int page = 1`) is **optional**.

**Binding a request body.** One complex parameter on POST/PUT/PATCH is read from the JSON body. Only **one** parameter can come from the body.

```csharp
app.MapPost("/todos", (NewTodo input) => Results.Created($"/todos/1", input));
```

A malformed or empty body (when one is required) yields 400.

**Grouping many parameters with `[AsParameters]`:**

```csharp
app.MapGet("/products", ([AsParameters] ProductQuery q) =>
    Results.Ok($"{q.Name} page {q.Page}"));

public record ProductQuery(string? Name, int Page = 1, int PageSize = 20);
```

**Uploading files:**

```csharp
app.MapPost("/upload", async (IFormFile file) =>
{
    await using var stream = File.Create(Path.Combine("uploads", file.FileName));
    await file.CopyToAsync(stream);
    return Results.Ok(new { file.FileName, file.Length });
}).DisableAntiforgery();   // required for form endpoints unless you add antiforgery tokens
```

(For production, never trust `file.FileName`. Generate your own file name and validate size and type.)

---

## 5.3 Results and filters

**Results.** The `Results` and `TypedResults` static classes build standard HTTP responses:

| Helper | Status |
|---|---|
| `Results.Ok(value)` | 200 |
| `Results.Created(uri, value)` | 201 |
| `Results.Accepted()` | 202 |
| `Results.NoContent()` | 204 |
| `Results.BadRequest(value)` | 400 |
| `Results.Unauthorized()` | 401 |
| `Results.Forbid()` | 403 |
| `Results.NotFound()` | 404 |
| `Results.Conflict()` | 409 |
| `Results.ValidationProblem(errors)` | 400 with problem details |
| `Results.Problem(...)` | RFC 9457 problem details |
| `Results.File(...)`, `Results.Redirect(...)`, `Results.Json(...)` | File, redirect, custom JSON |

**`TypedResults` vs `Results`.** `TypedResults.Ok(todo)` returns a concrete type (`Ok<Todo>`). That gives you compile-time type information, easier unit tests, and automatic OpenAPI response metadata. When a handler can return several outcomes, declare a union with `Results<...>`:

```csharp
using Microsoft.AspNetCore.Http.HttpResults;

app.MapGet("/todos/{id:int}",
    Results<Ok<Todo>, NotFound> (int id) =>
        todos.FirstOrDefault(t => t.Id == id) is Todo t
            ? TypedResults.Ok(t)
            : TypedResults.NotFound());
```

**Endpoint filters** are like middleware, but for a **single endpoint or group**. They run just before and after the handler and can inspect/modify the arguments or the result:

```csharp
app.MapPost("/todos", (NewTodo input) => TypedResults.Ok(input))
   .AddEndpointFilter(async (context, next) =>
   {
       var input = context.GetArgument<NewTodo>(0);     // read an argument by position

       if (string.IsNullOrWhiteSpace(input.Title))
       {
           return Results.ValidationProblem(new Dictionary<string, string[]>
           {
               ["title"] = new[] { "Title is required." }
           });
       }

       return await next(context);                      // call the handler
   });
```

Filters can also be classes implementing `IEndpointFilter`, so they are reusable:

```csharp
public class LoggingFilter : IEndpointFilter
{
    public async ValueTask<object?> InvokeAsync(
        EndpointFilterInvocationContext context, EndpointFilterDelegate next)
    {
        Console.WriteLine($"Calling {context.HttpContext.Request.Path}");
        return await next(context);
    }
}

app.MapGroup("/todos").AddEndpointFilter<LoggingFilter>();
```

Use filters for cross-cutting concerns tied to endpoints (validation, logging, idempotency). Use middleware for concerns that apply to **all** requests.

**Metadata for documentation** (used by OpenAPI, Lesson 18):

```csharp
app.MapGet("/todos", () => todos)
   .WithName("ListTodos")
   .WithSummary("Lists all todos")
   .WithTags("Todos")
   .Produces<List<Todo>>(StatusCodes.Status200OK);
```

---

## 5.4 Organizing a minimal API

Putting hundreds of lines in `Program.cs` becomes unmanageable. Move endpoints into **extension methods** per feature, using route groups.

**Suggested layout:**

```
MyApi/
├── Program.cs
├── Endpoints/
│   ├── TodoEndpoints.cs
│   └── UserEndpoints.cs
├── Models/
│   └── Todo.cs
└── Services/
    ├── ITodoService.cs
    └── TodoService.cs
```

**`Endpoints/TodoEndpoints.cs`:**

```csharp
using Microsoft.AspNetCore.Http.HttpResults;

public static class TodoEndpoints
{
    public static RouteGroupBuilder MapTodoEndpoints(this IEndpointRouteBuilder routes)
    {
        var group = routes.MapGroup("/todos").WithTags("Todos");

        group.MapGet("/", GetAll);
        group.MapGet("/{id:int}", GetById);
        group.MapPost("/", Create);

        return group;
    }

    private static Ok<List<Todo>> GetAll(ITodoService service)
        => TypedResults.Ok(service.GetAll());

    private static Results<Ok<Todo>, NotFound> GetById(int id, ITodoService service)
        => service.Find(id) is Todo todo ? TypedResults.Ok(todo) : TypedResults.NotFound();

    private static Created<Todo> Create(NewTodo input, ITodoService service)
    {
        var todo = service.Add(input.Title);
        return TypedResults.Created($"/todos/{todo.Id}", todo);
    }
}
```

**`Program.cs` stays short:**

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddSingleton<ITodoService, TodoService>();

var app = builder.Build();

app.MapGroup("/api").MapTodoEndpoints();    // → /api/todos

app.Run();
```

**Guidelines:**

- One static class per feature/resource; one method per endpoint.
- Inject services into handler parameters, and keep business logic in services, not in the endpoint (endpoints should translate HTTP to a service call and back).
- Apply shared concerns (auth, tags, filters) once on the group.
- Minimal APIs and controllers can live in the same app. Choose per feature, not per project.

---

[Previous](./[4]-Routing-And-Endpoints.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[6]-MVC-Controllers.md)
