[Previous](./[7]-Views-And-Razor-Syntax.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[9]-Model-Binding-And-Validation.md)

*Server-Side Development*

# Lesson 8 - Razor Pages

## 8.1 Page model

**Razor Pages** is a page-focused way to build server-rendered web apps. Instead of separating a controller and a view, each **page** pairs a Razor markup file with a **PageModel** class that holds its logic. The URL is derived from the file's location.

**Setup:**

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddRazorPages();

var app = builder.Build();
app.UseStaticFiles();
app.UseRouting();
app.UseAuthorization();
app.MapRazorPages();
app.Run();
```

**Folder layout** (routes come from the path under `Pages/`):

```
Pages/
├── _ViewImports.cshtml
├── _ViewStart.cshtml
├── Shared/_Layout.cshtml
├── Index.cshtml              → /
├── About.cshtml              → /About
└── Products/
    ├── Index.cshtml          → /Products
    ├── Details.cshtml        → /Products/Details
    └── Create.cshtml         → /Products/Create
```

**A page has two files:**

`Pages/Products/Index.cshtml` (markup):

```cshtml
@page
@model MyApp.Pages.Products.IndexModel
@{
    ViewData["Title"] = "Products";
}

<h1>Products</h1>
<ul>
@foreach (var p in Model.Products)
{
    <li>@p.Name - <a asp-page="Details" asp-route-id="@p.Id">Details</a></li>
}
</ul>
```

`Pages/Products/Index.cshtml.cs` (page model):

```csharp
namespace MyApp.Pages.Products;

public class IndexModel : PageModel
{
    private readonly IProductService _products;
    public IndexModel(IProductService products) => _products = products;

    public List<Product> Products { get; private set; } = new();

    public void OnGet()
    {
        Products = _products.GetAll();
    }
}
```

Key points:

- **`@page`** must be the first directive. It turns the file into an endpoint. Without it, the file is just a view.
- The **page model** derives from `PageModel`. Its public properties are available in the markup via `Model`.
- Constructor injection works the same as in controllers.
- Folders and files named `Index` map to the folder URL.

---

## 8.2 Handlers

**Handlers** are methods on the page model that respond to HTTP verbs. They are named `On` + verb, optionally with `Async`: `OnGet`, `OnPost`, `OnPut`, `OnDelete`, `OnGetAsync`, `OnPostAsync`.

**A form page** (`Pages/Products/Create.cshtml`):

```cshtml
@page
@model MyApp.Pages.Products.CreateModel

<h1>New product</h1>

<form method="post">
    <div>
        <label asp-for="Input.Name"></label>
        <input asp-for="Input.Name" />
        <span asp-validation-for="Input.Name"></span>
    </div>
    <div>
        <label asp-for="Input.Price"></label>
        <input asp-for="Input.Price" />
        <span asp-validation-for="Input.Price"></span>
    </div>
    <button type="submit">Create</button>
</form>
```

```csharp
public class CreateModel : PageModel
{
    private readonly IProductService _products;
    public CreateModel(IProductService products) => _products = products;

    [BindProperty]                                   // bound from the posted form
    public ProductInput Input { get; set; } = new();

    public void OnGet() { }                          // show an empty form

    public async Task<IActionResult> OnPostAsync()
    {
        if (!ModelState.IsValid) return Page();      // redisplay with validation errors

        await _products.AddAsync(Input);
        TempData["Message"] = "Created.";
        return RedirectToPage("Index");              // Post-Redirect-Get
    }
}

public class ProductInput
{
    [Required, StringLength(100)]
    public string Name { get; set; } = "";

    [Range(0.01, 100000)]
    public decimal Price { get; set; }
}
```

**Important details:**

- **`[BindProperty]`** binds a property from the request. By default it applies to **non-GET** requests only. Use `[BindProperty(SupportsGet = true)]` to bind on GET (e.g. search filters from the query string).
- Handlers return `void` (render the page), `IActionResult`, or `Task<IActionResult>`. Common results: `Page()`, `RedirectToPage(...)`, `NotFound()`, `Forbid()`, `File(...)`.
- **Antiforgery** validation is on **by default** for Razor Pages POSTs, and the form tag helper adds the token automatically.

**Named handlers** let one page handle several buttons. Name them `OnPost{Name}`:

```csharp
public async Task<IActionResult> OnPostDeleteAsync(int id)
{
    await _products.RemoveAsync(id);
    return RedirectToPage();
}
```

```html
<form method="post" asp-page-handler="Delete" asp-route-id="@p.Id">
    <button type="submit">Delete</button>
</form>
```

This generates the URL `?handler=Delete&id=...`.

---

## 8.3 Routing in Razor Pages

By default the route is the **file path** below `Pages/`. Add a route template after `@page` to customize it or add parameters.

```cshtml
@page "{id:int}"
@model DetailsModel
```

`Pages/Products/Details.cshtml` with `@page "{id:int}"` handles `/Products/Details/5`. The parameter is passed to the handler by name:

```csharp
public class DetailsModel : PageModel
{
    public Product? Product { get; private set; }

    public IActionResult OnGet(int id, [FromServices] IProductService products)
    {
        Product = products.Find(id);
        return Product is null ? NotFound() : Page();
    }
}
```

**Other route features:**

| Syntax | Effect |
|---|---|
| `@page "{id:int?}"` | Optional route parameter with a constraint |
| `@page "/shop/item/{id}"` | A leading `/` makes the route **absolute** (ignores the folder path) |
| `@page "{slug?}"` | Optional parameter |
| `@page` | Route = file path only |

All the route constraints from Lesson 4 work here too.

**Linking between pages** with the `asp-page` tag helper:

```html
<a asp-page="/Index">Home</a>
<a asp-page="/Products/Details" asp-route-id="5">Product 5</a>
<a asp-page="Details" asp-route-id="5">Relative link, same folder</a>
```

In code: `RedirectToPage("/Products/Details", new { id = 5 })`.

**Customizing conventions** (for example, to protect a folder) is done in `AddRazorPages`:

```csharp
builder.Services.AddRazorPages(options =>
{
    options.Conventions.AuthorizeFolder("/Admin");
    options.Conventions.AllowAnonymousToPage("/Admin/Login");
});
```

---

## 8.4 When to choose Razor Pages

ASP.NET Core offers several UI/endpoint styles. They can coexist in one app.

| Approach | Best for | Trade-off |
|---|---|---|
| **Razor Pages** | Page-centric sites: forms, CRUD screens, content sites, admin panels | Less suited to JSON APIs |
| **MVC (controllers + views)** | Larger server-rendered apps with shared controller logic, or teams used to MVC | More files and ceremony per feature |
| **Minimal APIs / API controllers** | JSON APIs for SPAs, mobile apps, other services | No server-rendered HTML |
| **Blazor** | Rich, interactive UI written in C# instead of JavaScript | Requires a hosting model decision; more complex runtime |

**Rule of thumb:**

- Building *pages* where each page does one thing → **Razor Pages**. The page and its logic stay together, so features are easy to find and change.
- Building an *API* → **Minimal APIs** or **API controllers**.
- Building a highly interactive app and wanting to avoid JavaScript → **Blazor**.
- MVC and Razor Pages share the same view engine, tag helpers, model binding, and validation, so skills transfer between them.

ASP.NET Core Identity's default UI (login, register, manage account) is built with Razor Pages, so you will meet them again in Lesson 15.

---

[Previous](./[7]-Views-And-Razor-Syntax.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[9]-Model-Binding-And-Validation.md)
