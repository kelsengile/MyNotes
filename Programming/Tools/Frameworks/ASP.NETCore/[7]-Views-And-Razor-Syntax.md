[Previous](./[6]-MVC-Controllers.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[8]-Razor-Pages.md)

*Server-Side Development*

# Lesson 7 - Views And Razor Syntax

## 7.1 Razor basics

**Razor** is a template syntax that mixes HTML with C#. Files end in `.cshtml`. The server runs the C# and sends plain HTML to the browser.

**View location conventions** (used by `return View()`):

```
Views/
├── _ViewImports.cshtml          ← shared @using / tag helper imports
├── _ViewStart.cshtml            ← code run before every view (sets the layout)
├── Shared/
│   ├── _Layout.cshtml
│   └── _ValidationScriptsPartial.cshtml
├── Home/
│   └── Index.cshtml             ← HomeController.Index()
└── Products/
    ├── Index.cshtml
    └── Details.cshtml
```

When `ProductsController.Details` returns `View(product)`, Razor looks for `Views/Products/Details.cshtml`, then `Views/Shared/Details.cshtml`.

**Core syntax:**

| Syntax | Meaning |
|---|---|
| `@name` | Output a C# expression (HTML-encoded) |
| `@(a + b)` | Explicit expression |
| `@{ ... }` | Code block (statements) |
| `@if`, `@foreach`, `@for`, `@switch` | Control flow |
| `@model Type` | Declares the view's model type |
| `@Model` | The model instance |
| `@* comment *@` | Razor comment (not sent to the browser) |
| `@@` | A literal `@` |
| `@using`, `@inject`, `@functions` | Directives |

**A complete view:**

```cshtml
@model List<Product>

@{
    ViewData["Title"] = "Products";
    var total = Model.Count;
}

<h1>@ViewData["Title"] (@total)</h1>

@if (Model.Any())
{
    <ul>
    @foreach (var p in Model)
    {
        <li>@p.Name - @p.Price.ToString("C")</li>
    }
    </ul>
}
else
{
    <p>No products yet.</p>
}
```

**Automatic HTML encoding.** `@p.Name` is HTML-encoded, so `<script>` in user data renders as harmless text. This protects against **cross-site scripting (XSS)**. Only use `@Html.Raw(value)` for content you fully trust:

```cshtml
@Html.Raw(trustedHtml)   @* dangerous with user input *@
```

**`_ViewImports.cshtml`** saves repeating directives in every view:

```cshtml
@using MyApp
@using MyApp.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

**`_ViewStart.cshtml`** runs before each view:

```cshtml
@{
    Layout = "_Layout";
}
```

**Passing data to views:** prefer a strongly typed `@model`. `ViewData["Key"]` and `ViewBag.Key` are loosely typed dictionaries, handy for small things like the page title. `TempData` survives one redirect.

**Injecting services into a view** (for presentation-only concerns):

```cshtml
@inject IConfiguration Config
<footer>@Config["AppName"]</footer>
```

---

## 7.2 Layouts and partials

**Layouts** hold markup shared by many pages (header, navigation, footer, script tags).

`Views/Shared/_Layout.cshtml`:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <title>@ViewData["Title"] - MyApp</title>
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
    @await RenderSectionAsync("Styles", required: false)
</head>
<body>
    <nav>
        <a asp-controller="Home" asp-action="Index">Home</a>
        <a asp-controller="Products" asp-action="Index">Products</a>
    </nav>

    <main>
        @RenderBody()                      @* the content of the individual view goes here *@
    </main>

    <footer>&copy; 2026 MyApp</footer>

    <script src="~/js/site.js"></script>
    @await RenderSectionAsync("Scripts", required: false)
</body>
</html>
```

- `@RenderBody()` is where each view's content is inserted. It appears exactly once.
- **Sections** let a view add content to specific places in the layout:

```cshtml
@* In a view *@
@section Scripts {
    <script src="~/js/products.js"></script>
}
```

- `~/` refers to the web root (`wwwroot`).
- A view can pick a different layout: `@{ Layout = "_AdminLayout"; }` or no layout: `Layout = null;`.

**Partial views** are reusable view fragments, rendered inside other views. By convention, their names start with an underscore.

`Views/Shared/_ProductCard.cshtml`:

```cshtml
@model Product

<div class="card">
    <h3>@Model.Name</h3>
    <p>@Model.Price.ToString("C")</p>
</div>
```

Use it:

```cshtml
@foreach (var p in Model)
{
    <partial name="_ProductCard" model="p" />
}
```

Alternative (async HTML helper): `@await Html.PartialAsync("_ProductCard", p)`.

A partial has no logic of its own; it only receives the data you pass. When you need logic or data access, use a **view component** (7.4).

---

## 7.3 Tag helpers

**Tag helpers** are server-side components that make HTML elements "smart" using special `asp-*` attributes. They look like normal HTML, which keeps views readable (unlike older `@Html.ActionLink(...)` helpers).

Enable them in `_ViewImports.cshtml`:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

**Links and URLs:**

```html
<a asp-controller="Products" asp-action="Details" asp-route-id="@p.Id">Details</a>
<a asp-page="/Products/Details" asp-route-id="@p.Id">Details</a>   <!-- Razor Pages -->
<img src="~/images/logo.png" asp-append-version="true" />          <!-- cache-busting ?v=hash -->
```

**Forms and inputs** (used with a strongly typed model):

```cshtml
@model ProductViewModel

<form asp-controller="Products" asp-action="Create" method="post">
    <div>
        <label asp-for="Name"></label>
        <input asp-for="Name" />
        <span asp-validation-for="Name" class="text-danger"></span>
    </div>

    <div>
        <label asp-for="CategoryId"></label>
        <select asp-for="CategoryId" asp-items="Model.Categories"></select>
    </div>

    <div asp-validation-summary="ModelOnly" class="text-danger"></div>
    <button type="submit">Save</button>
</form>
```

What they do for you:

| Tag helper | Effect |
|---|---|
| `<form asp-action>` | Builds the `action` URL and **adds an antiforgery token** (protects against CSRF) |
| `<input asp-for="Name">` | Sets `name`, `id`, `value`, the correct `type` (email, number, checkbox...) and validation data attributes from the model |
| `<label asp-for>` | Uses the `[Display]` name or property name |
| `<span asp-validation-for>` | Shows the validation message for that field |
| `<div asp-validation-summary>` | Lists validation errors |
| `<select asp-for asp-items>` | Builds `<option>` elements from a list |

**Environment tag helper** (render different markup in different environments):

```html
<environment include="Development">
    <script src="~/lib/jquery/jquery.js"></script>
</environment>
<environment exclude="Development">
    <script src="~/lib/jquery/jquery.min.js"></script>
</environment>
```

**Custom tag helper** (a simple example turning `<email address="a@b.com">` into a mailto link):

```csharp
[HtmlTargetElement("email")]
public class EmailTagHelper : TagHelper
{
    public string Address { get; set; } = "";

    public override void Process(TagHelperContext context, TagHelperOutput output)
    {
        output.TagName = "a";
        output.Attributes.SetAttribute("href", $"mailto:{Address}");
        output.Content.SetContent(Address);
    }
}
```

```cshtml
@addTagHelper *, MyApp
<email address="support@example.com"></email>
```

---

## 7.4 View components

A **view component** is like a mini-controller plus view: a reusable chunk of UI that **has its own logic and can fetch its own data**. Use one when a partial view isn't enough (e.g. a shopping-cart summary, a "latest posts" sidebar, a navigation menu loaded from a database).

**1. The class** (`ViewComponents/CartSummaryViewComponent.cs`):

```csharp
public class CartSummaryViewComponent : ViewComponent
{
    private readonly ICartService _cart;
    public CartSummaryViewComponent(ICartService cart) => _cart = cart;

    public async Task<IViewComponentResult> InvokeAsync(int maxItems = 3)
    {
        var items = await _cart.GetItemsAsync(maxItems);
        return View(items);                      // looks for the "Default" view
    }
}
```

**2. The view** at one of these paths:

```
Views/Shared/Components/CartSummary/Default.cshtml
Views/{Controller}/Components/CartSummary/Default.cshtml
```

```cshtml
@model IEnumerable<CartItem>

<div class="cart-summary">
    <p>@Model.Count() item(s) in your cart</p>
</div>
```

**3. Use it in any view or layout:**

```cshtml
@await Component.InvokeAsync("CartSummary", new { maxItems = 5 })
```

or with tag-helper syntax (requires `@addTagHelper *, MyApp` in `_ViewImports`; PascalCase name becomes kebab-case with `vc:` prefix):

```cshtml
<vc:cart-summary max-items="5"></vc:cart-summary>
```

**Partial view vs view component:**

| | Partial view | View component |
|---|---|---|
| Has its own logic / data access | No | Yes (DI, async) |
| Receives data from | The calling view | Its own parameters and services |
| Is it an endpoint? | No | No (rendered only from a view) |
| Best for | Repeating markup | Self-contained widgets |

---

[Previous](./[6]-MVC-Controllers.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[8]-Razor-Pages.md)
