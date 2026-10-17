[Previous](./[5]-Minimal-APIs.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[7]-Views-And-Razor-Syntax.md)

*Server-Side Development*

# Lesson 6 - MVC Controllers

## 6.1 The MVC pattern

**MVC (Model-View-Controller)** separates an app into three roles:

| Role | Responsibility | In ASP.NET Core |
|---|---|---|
| **Model** | Data and business rules | Plain C# classes, EF Core entities, view models |
| **View** | The HTML (or other output) shown to the user | `.cshtml` Razor files (Lesson 7) |
| **Controller** | Receives the request, uses the model, picks the response | Classes ending in `Controller` |

```
Request ─▶ Routing ─▶ Controller action ─▶ uses Model/Services
                              │
                              ▼
                        returns a View (HTML)  or  data (JSON)
```

**Flow of a page request** (`GET /Products/Details/5`):

1. Routing selects `ProductsController.Details(int id)`.
2. The action loads the product (through a service or `DbContext`).
3. It returns `View(product)`; the Razor view renders HTML using that product.

**Setting up MVC with views:**

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddControllersWithViews();      // controllers + Razor views

var app = builder.Build();
app.UseStaticFiles();
app.UseRouting();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();
```

For an **API-only** app (no views), use `AddControllers()` and `MapControllers()` instead.

**Conventional routing** (above) derives the URL from the controller and action names: `/Products/Details/5` → `ProductsController.Details(5)`. **Attribute routing** declares routes directly on controllers/actions and is the standard for APIs (see 6.4).

---

## 6.2 Controllers and actions

A **controller** is a class that groups related actions. By convention:

- It lives in a `Controllers/` folder.
- Its name ends with `Controller` and it derives from `Controller` (for views) or `ControllerBase` (for APIs).
- Its **public methods** are **actions**. Each handles one kind of request.

```csharp
using Microsoft.AspNetCore.Mvc;

public class ProductsController : Controller
{
    private readonly IProductService _products;

    public ProductsController(IProductService products)    // dependency injection
    {
        _products = products;
    }

    // GET /Products
    public IActionResult Index()
    {
        var list = _products.GetAll();
        return View(list);                                   // renders Views/Products/Index.cshtml
    }

    // GET /Products/Details/5
    public IActionResult Details(int id)
    {
        var product = _products.Find(id);
        if (product is null) return NotFound();
        return View(product);
    }

    // GET /Products/Create (show the form)
    [HttpGet]
    public IActionResult Create() => View();

    // POST /Products/Create (handle the form)
    [HttpPost]
    [ValidateAntiForgeryToken]
    public IActionResult Create(ProductViewModel model)
    {
        if (!ModelState.IsValid) return View(model);         // redisplay with errors

        _products.Add(model);
        return RedirectToAction(nameof(Index));              // Post-Redirect-Get pattern
    }
}
```

**HTTP verb attributes** restrict which method an action accepts: `[HttpGet]`, `[HttpPost]`, `[HttpPut]`, `[HttpDelete]`, `[HttpPatch]`. Two actions can share a name if they differ by verb (like the two `Create` actions).

**Mark a public method as not-an-action** with `[NonAction]`.

**Useful members inherited from `Controller`/`ControllerBase`:**

| Member | Purpose |
|---|---|
| `Request`, `Response`, `HttpContext` | Raw HTTP access |
| `User` | The current `ClaimsPrincipal` |
| `ModelState` | Binding and validation state |
| `RouteData`, `Url` | Route values and link generation |
| `TempData`, `ViewData`, `ViewBag` | Passing data to views / across redirects |

**Keep controllers thin.** A controller should translate HTTP into a call to a service and the result into a response. Put business logic in services.

---

## 6.3 Action results

Actions return an **action result** that tells the framework how to build the response. The base type is `IActionResult`.

| Helper method | Status / output |
|---|---|
| `View()` / `View(model)` | HTML from a Razor view (200) |
| `PartialView(...)` | HTML fragment |
| `Ok(value)` | 200 with serialized body |
| `Json(value)` | 200 JSON |
| `Content("text")` | 200 plain text/HTML |
| `Created(uri, value)` / `CreatedAtAction(...)` | 201 |
| `NoContent()` | 204 |
| `RedirectToAction(...)`, `RedirectToPage(...)`, `Redirect(url)` | 302 redirect |
| `LocalRedirect(url)` | Redirect, only to local URLs (prevents open redirects) |
| `BadRequest(...)` | 400 |
| `Unauthorized()` / `Forbid()` | 401 / 403 |
| `NotFound()` | 404 |
| `Conflict()` | 409 |
| `File(...)`, `PhysicalFile(...)` | File download |
| `StatusCode(int)` | Any status code |

**Choosing a return type:**

```csharp
public IActionResult Get(int id)                       // flexible: any result
{
    var p = _products.Find(id);
    return p is null ? NotFound() : Ok(p);
}

public ActionResult<Product> GetTyped(int id)          // keeps the type for OpenAPI
{
    var p = _products.Find(id);
    if (p is null) return NotFound();
    return p;                                          // implicit conversion to Ok(p)
}

public async Task<ActionResult<List<Product>>> GetAll()   // async version
    => await _products.GetAllAsync();
```

- Use **`IActionResult`** when returning views or mixed results.
- Use **`ActionResult<T>`** in API actions so the response type is documented and checked at compile time.
- Always use **`async`/`Task`** when doing I/O (database, HTTP calls). See Lesson 20.

**Post-Redirect-Get.** After a successful form POST, *redirect* rather than returning a view. This prevents the browser from resubmitting the form on refresh. Use `TempData` for a one-time message:

```csharp
TempData["Message"] = "Product saved.";
return RedirectToAction(nameof(Index));
```

---

## 6.4 API controllers

For JSON APIs, derive from `ControllerBase` (no view support) and decorate the class with **`[ApiController]`**.

```csharp
[ApiController]
[Route("api/[controller]")]                  // → /api/todos
public class TodosController : ControllerBase
{
    private readonly ITodoService _service;
    public TodosController(ITodoService service) => _service = service;

    [HttpGet]
    public ActionResult<IEnumerable<Todo>> GetAll() => Ok(_service.GetAll());

    [HttpGet("{id:int}")]
    public ActionResult<Todo> GetById(int id)
    {
        var todo = _service.Find(id);
        return todo is null ? NotFound() : todo;
    }

    [HttpPost]
    public ActionResult<Todo> Create(NewTodo input)         // body bound automatically
    {
        var todo = _service.Add(input.Title);
        return CreatedAtAction(nameof(GetById), new { id = todo.Id }, todo);
    }

    [HttpDelete("{id:int}")]
    public IActionResult Delete(int id)
        => _service.Remove(id) ? NoContent() : NotFound();
}

public record Todo(int Id, string Title, bool Done);
public record NewTodo(string Title);
```

Register with `builder.Services.AddControllers();` and `app.MapControllers();`.

**Attribute routing** tokens: `[controller]` is replaced by the controller name without the `Controller` suffix; `[action]` by the action name. A route on the class is combined with routes on the actions.

**What `[ApiController]` adds automatically:**

| Behavior | Effect |
|---|---|
| **Automatic HTTP 400** | If model validation fails, the action never runs; a `ValidationProblemDetails` response is returned. No need to check `ModelState.IsValid`. |
| **Binding source inference** | Complex types come from the body, simple types from route/query. No `[FromBody]` needed. |
| **Attribute routing required** | Actions must be reachable via attribute routes. |
| **Problem details** for error status codes | 404/400 etc. return a standard problem JSON shape. |

**Registering both styles:** an app can mix `MapControllers()` with minimal API endpoints. Use controllers when you want built-in conventions, filters, and model validation; minimal APIs for lean, focused endpoints.

---

[Previous](./[5]-Minimal-APIs.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[7]-Views-And-Razor-Syntax.md)
