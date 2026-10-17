[Previous](./[8]-Razor-Pages.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[10]-Blazor-Components.md)

*Server-Side Development*

# Lesson 9 - Model Binding And Validation

## 9.1 Binding sources

**Model binding** converts raw request data (strings from the URL, form, headers, JSON body) into typed .NET parameters and objects. **Validation** then checks that data.

**Where data can come from:**

| Source | Attribute | Typical use |
|---|---|---|
| Route values | `[FromRoute]` | `/products/5` → `id` |
| Query string | `[FromQuery]` | `?page=2&sort=name` |
| Form fields | `[FromForm]` | HTML form posts |
| JSON body | `[FromBody]` | API POST/PUT |
| Headers | `[FromHeader]` | `X-Api-Key` |
| DI services | `[FromServices]` | Inject a service into an action |

Without attributes, MVC/Razor Pages search **form values → route values → query string** in that order. With `[ApiController]`, complex types are assumed to come from the body.

```csharp
[HttpGet("search/{category}")]
public IActionResult Search(
    [FromRoute] string category,
    [FromQuery] string? q,
    [FromQuery] int page = 1,
    [FromHeader(Name = "X-Client")] string? client)
    => Ok(new { category, q, page, client });

[HttpPost]
public IActionResult Create([FromBody] CreateOrderRequest request)
    => Ok(request);
```

**Complex type binding** matches property names (case-insensitive) to incoming keys:

```csharp
public class OrderFilter
{
    public string? Status { get; set; }
    public int Page { get; set; } = 1;
    public List<int> CategoryIds { get; set; } = new();
}

// GET /orders?status=open&page=2&categoryIds=1&categoryIds=4
public IActionResult Index([FromQuery] OrderFilter filter) => Ok(filter);
```

Collections bind from repeated keys (`categoryIds=1&categoryIds=4`) or indexed form fields (`Items[0].Name`).

**Binding is forgiving; failures don't throw.** If a value cannot be converted (e.g. `"abc"` to `int`), binding records an error in **`ModelState`** and continues. It is up to you (or `[ApiController]`) to check it.

**Over-posting protection.** A client can post extra fields. If you bind directly to an entity (say with an `IsAdmin` property), an attacker could set it. Avoid this by binding to a **dedicated view model / DTO** that contains only the fields the user may set:

```csharp
// BAD: binds every property of the entity, including IsAdmin
public IActionResult Register(User user) { ... }

// GOOD: binds only what the form is allowed to send
public IActionResult Register(RegisterViewModel model) { ... }
```

---

## 9.2 Data annotations

**Data annotations** are attributes that declare validation rules on model properties. They are in `System.ComponentModel.DataAnnotations`.

```csharp
using System.ComponentModel.DataAnnotations;

public class RegisterViewModel
{
    [Required(ErrorMessage = "Please enter your name.")]
    [StringLength(50, MinimumLength = 2)]
    [Display(Name = "Full name")]
    public string Name { get; set; } = "";

    [Required, EmailAddress]
    public string Email { get; set; } = "";

    [Required, MinLength(8)]
    [DataType(DataType.Password)]
    public string Password { get; set; } = "";

    [Compare(nameof(Password), ErrorMessage = "Passwords do not match.")]
    [DataType(DataType.Password)]
    public string ConfirmPassword { get; set; } = "";

    [Range(18, 120)]
    public int Age { get; set; }

    [RegularExpression(@"^\d{5}$", ErrorMessage = "Enter a 5-digit ZIP code.")]
    public string? Zip { get; set; }

    [Url]
    public string? Website { get; set; }
}
```

| Attribute | Rule |
|---|---|
| `[Required]` | Value must be present (non-null; strings must be non-empty) |
| `[StringLength(max, MinimumLength = n)]` | Length bounds |
| `[MinLength]`, `[MaxLength]` | Length bounds for strings/collections |
| `[Range(min, max)]` | Numeric/date range |
| `[EmailAddress]`, `[Phone]`, `[Url]`, `[CreditCard]` | Format checks |
| `[RegularExpression]` | Pattern match |
| `[Compare("Other")]` | Equals another property |
| `[Display(Name = "...")]` | Label text (not a rule) |

**Nullable reference types and `[Required]`:** with `<Nullable>enable</Nullable>`, a non-nullable `string` property is treated as required automatically. Initialize strings (`= ""`) to avoid compiler warnings, and still add `[Required]` for clear error messages.

**Checking validation in an MVC action or Razor Page:**

```csharp
[HttpPost]
public IActionResult Register(RegisterViewModel model)
{
    if (!ModelState.IsValid)
        return View(model);          // re-render the form; tag helpers show the errors

    // ... create the user
    return RedirectToAction("Index");
}
```

You can add your own errors: `ModelState.AddModelError("Email", "That email is already used.");`

**Custom attribute:**

```csharp
public class NotInPastAttribute : ValidationAttribute
{
    protected override ValidationResult? IsValid(object? value, ValidationContext context)
    {
        if (value is DateTime date && date.Date < DateTime.Today)
            return new ValidationResult("Date cannot be in the past.");
        return ValidationResult.Success;
    }
}
```

**Cross-property rules** with `IValidatableObject`:

```csharp
public class Booking : IValidatableObject
{
    public DateTime Start { get; set; }
    public DateTime End { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext context)
    {
        if (End <= Start)
            yield return new ValidationResult("End must be after Start.", new[] { nameof(End) });
    }
}
```

**Client-side validation.** The MVC/Razor templates include jQuery Validation, and tag helpers emit `data-val-*` attributes so basic rules run in the browser before submit. **Always keep server-side validation**, because client checks can be bypassed.

---

## 9.3 FluentValidation

**FluentValidation** is a popular third-party library that moves validation rules into separate classes using a readable, fluent API. It handles complex, conditional, or reusable rules better than attributes and keeps your models clean.

**Install:**

```bash
dotnet add package FluentValidation.DependencyInjectionExtensions
```

**Define a validator:**

```csharp
using FluentValidation;

public class CreateProductRequest
{
    public string Name { get; set; } = "";
    public decimal Price { get; set; }
    public string? Sku { get; set; }
}

public class CreateProductValidator : AbstractValidator<CreateProductRequest>
{
    public CreateProductValidator()
    {
        RuleFor(x => x.Name)
            .NotEmpty().WithMessage("Name is required.")
            .MaximumLength(100);

        RuleFor(x => x.Price)
            .GreaterThan(0);

        RuleFor(x => x.Sku)
            .Matches("^[A-Z]{3}-\\d{4}$")
            .When(x => !string.IsNullOrEmpty(x.Sku));    // conditional rule
    }
}
```

**Register all validators in the assembly:**

```csharp
builder.Services.AddValidatorsFromAssemblyContaining<CreateProductValidator>();
```

**Use it.** Current FluentValidation versions no longer auto-run during model binding, so you invoke validators explicitly by injecting `IValidator<T>`:

```csharp
app.MapPost("/products", async (CreateProductRequest request,
                                IValidator<CreateProductRequest> validator) =>
{
    var result = await validator.ValidateAsync(request);
    if (!result.IsValid)
        return Results.ValidationProblem(result.ToDictionary());

    // ... save
    return Results.Created("/products/1", request);
});
```

`result.ToDictionary()` is an extension method (in the `FluentValidation` namespace) that returns `Dictionary<string, string[]>`, which `Results.ValidationProblem` expects.

**Reusing the logic as an endpoint filter** so every endpoint doesn't repeat the code:

```csharp
public class ValidationFilter<T> : IEndpointFilter where T : class
{
    public async ValueTask<object?> InvokeAsync(
        EndpointFilterInvocationContext context, EndpointFilterDelegate next)
    {
        var validator = context.HttpContext.RequestServices.GetService<IValidator<T>>();
        var model = context.Arguments.OfType<T>().FirstOrDefault();

        if (validator is not null && model is not null)
        {
            var result = await validator.ValidateAsync(model);
            if (!result.IsValid)
                return Results.ValidationProblem(result.ToDictionary());
        }
        return await next(context);
    }
}

app.MapPost("/products", (CreateProductRequest r) => Results.Ok(r))
   .AddEndpointFilter<ValidationFilter<CreateProductRequest>>();
```

**Attributes vs FluentValidation:**

| | Data annotations | FluentValidation |
|---|---|---|
| Setup | Built in | NuGet package |
| Rules live | On the model | In a separate validator class |
| Conditional / cross-field rules | Awkward | Natural |
| Async rules (e.g. database check) | Limited | Supported |
| Client-side validation (MVC) | Built in | Limited |

Note: newer .NET versions also add built-in validation support for minimal APIs using data annotations. Check the current official documentation for your target version.

---

## 9.4 Validation responses

How validation failures are reported depends on the app style.

**Server-rendered pages (MVC / Razor Pages).** The action re-renders the same view; tag helpers display messages from `ModelState`. Return **HTTP 200** with the form again (or 400 is fine too). See 9.2.

**API controllers with `[ApiController]`.** Invalid models are rejected **automatically with 400** before your action runs. The body follows RFC 9457 / `ValidationProblemDetails`:

```json
{
  "type": "https://tools.ietf.org/html/rfc9110#section-15.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Email": ["The Email field is not a valid e-mail address."],
    "Age": ["The field Age must be between 18 and 120."]
  }
}
```

**Minimal APIs.** Return the same shape yourself:

```csharp
return Results.ValidationProblem(new Dictionary<string, string[]>
{
    ["email"] = new[] { "Email is invalid." }
});
// or: TypedResults.ValidationProblem(errors)
```

**Customizing the automatic 400 response in controllers:**

```csharp
builder.Services.AddControllers()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            var errors = context.ModelState
                .Where(e => e.Value?.Errors.Count > 0)
                .ToDictionary(
                    e => e.Key,
                    e => e.Value!.Errors.Select(x => x.ErrorMessage).ToArray());

            return new BadRequestObjectResult(new { message = "Validation failed", errors });
        };
    });
```

To turn the automatic behavior off, set `options.SuppressModelStateInvalidFilter = true;`.

**Choosing status codes:**

| Situation | Status |
|---|---|
| Malformed JSON, missing/invalid fields | **400 Bad Request** |
| Well-formed but violates a business rule (e.g. duplicate email) | **409 Conflict** or **422 Unprocessable Content** |
| Resource doesn't exist | **404 Not Found** |

Pick one convention for your API and apply it consistently. Clients should be able to read the `errors` dictionary and show messages next to the right field.

---

[Previous](./[8]-Razor-Pages.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[10]-Blazor-Components.md)
