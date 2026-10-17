[Previous](./[15]-Authentication-And-Identity.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[17]-JWT-And-API-Security.md)

*Security*

# Lesson 16 - Authorization

## 16.1 Roles

**Authorization** decides whether an **authenticated** user may perform an action. It always runs after authentication, so the middleware order is `UseAuthentication()` then `UseAuthorization()` (Lesson 3).

**The basic attributes:**

```csharp
[Authorize]                          // must be logged in
public class AccountController : Controller
{
    public IActionResult Profile() => View();

    [AllowAnonymous]                 // exception: anyone may access this action
    public IActionResult Login() => View();
}
```

An unauthenticated request gets **401 Unauthorized** (or a redirect to the login page for cookie auth). An authenticated user who lacks permission gets **403 Forbidden** (or a redirect to the access-denied page).

**Roles** are named groups such as `Admin`, `Manager`, `Customer`. A user can belong to several.

**Enable roles in Identity:**

```csharp
builder.Services.AddIdentity<ApplicationUser, IdentityRole>()
    .AddEntityFrameworkStores<AppDbContext>()
    .AddDefaultTokenProviders();
// With AddDefaultIdentity, use: .AddRoles<IdentityRole>()
```

**Creating roles and assigning them:**

```csharp
using var scope = app.Services.CreateScope();
var roles = scope.ServiceProvider.GetRequiredService<RoleManager<IdentityRole>>();
var users = scope.ServiceProvider.GetRequiredService<UserManager<ApplicationUser>>();

foreach (var role in new[] { "Admin", "Manager", "Customer" })
    if (!await roles.RoleExistsAsync(role))
        await roles.CreateAsync(new IdentityRole(role));

var admin = await users.FindByEmailAsync("admin@example.com");
if (admin is not null && !await users.IsInRoleAsync(admin, "Admin"))
    await users.AddToRoleAsync(admin, "Admin");
```

**Protecting endpoints by role:**

```csharp
[Authorize(Roles = "Admin")]                       // must be Admin
public IActionResult DeleteEverything() => Ok();

[Authorize(Roles = "Admin,Manager")]               // Admin OR Manager
public IActionResult Reports() => Ok();

[Authorize(Roles = "Admin")]
[Authorize(Roles = "Manager")]                     // stacked: Admin AND Manager
public IActionResult TopSecret() => Ok();

// Minimal API
app.MapGet("/admin", () => "Admin area").RequireAuthorization(p => p.RequireRole("Admin"));
```

**Checking roles in code or views:**

```csharp
if (User.IsInRole("Admin")) { ... }
```

```cshtml
@if (User.IsInRole("Admin"))
{
    <a asp-controller="Admin" asp-action="Index">Admin panel</a>
}
```

Hiding a link is **not security**. Always protect the endpoint itself too.

**Limits of roles:** they are simple but coarse. "Can edit *only their own* posts" or "must be over 18" cannot be expressed as a role. For those, use claims and policies.

---

## 16.2 Claims

A **claim** is a statement about a user: a name/value pair such as `Email = alice@x.com`, `Role = Admin`, `Department = Sales`, `BirthDate = 1990-01-01`. Claims are the underlying model; **roles are just claims** with the type `ClaimTypes.Role`.

The authenticated user is a `ClaimsPrincipal`, which contains one or more `ClaimsIdentity` objects, each holding a list of `Claim`s:

```
ClaimsPrincipal (HttpContext.User)
 └─ ClaimsIdentity (e.g. authenticated via "Cookies")
      ├─ Claim: NameIdentifier = "42"
      ├─ Claim: Name           = "alice"
      ├─ Claim: Email          = "alice@x.com"
      └─ Claim: Role           = "Admin"
```

**Reading claims:**

```csharp
string? userId = User.FindFirstValue(ClaimTypes.NameIdentifier);
string? email  = User.FindFirstValue(ClaimTypes.Email);
bool isAdmin   = User.IsInRole("Admin");
bool authed    = User.Identity?.IsAuthenticated ?? false;

foreach (var claim in User.Claims)
    Console.WriteLine($"{claim.Type} = {claim.Value}");

// Minimal API: inject the principal directly
app.MapGet("/me", (ClaimsPrincipal user) => new { Name = user.Identity?.Name });
```

**Adding claims to a user** (stored in the database with Identity):

```csharp
await userManager.AddClaimAsync(user, new Claim("Department", "Sales"));
await userManager.AddClaimAsync(user, new Claim("EmployeeLevel", "5"));
```

These claims are included in the cookie (or token) at the **next sign-in**. A user must sign in again (or refresh the security stamp) to see changes.

**Customizing claims at sign-in** with a claims factory:

```csharp
public class AppClaimsFactory : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AppClaimsFactory(UserManager<ApplicationUser> um, RoleManager<IdentityRole> rm,
                            IOptions<IdentityOptions> options) : base(um, rm, options) { }

    protected override async Task<ClaimsIdentity> GenerateClaimsAsync(ApplicationUser user)
    {
        var identity = await base.GenerateClaimsAsync(user);
        identity.AddClaim(new Claim("DisplayName", user.DisplayName ?? user.UserName!));
        return identity;
    }
}

builder.Services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, AppClaimsFactory>();
```

**Claims-based authorization** is done through policies (next section). Keep claims small: they travel with every request in a cookie or token.

---

## 16.3 Policy-based authorization

A **policy** is a named set of requirements. It separates *what the rule is* from *where it is applied*, and is the recommended approach for anything beyond simple roles.

**Defining policies:**

```csharp
builder.Services.AddAuthorizationBuilder()
    .AddPolicy("AdminOnly", p => p.RequireRole("Admin"))
    .AddPolicy("SalesStaff", p => p.RequireClaim("Department", "Sales", "Marketing"))
    .AddPolicy("HasEmployeeId", p => p.RequireClaim("EmployeeId"))
    .AddPolicy("SeniorAdmin", p => p.RequireRole("Admin").RequireClaim("EmployeeLevel", "5", "6"));
```

(`AddAuthorizationBuilder()` is available from .NET 8; before that, use `builder.Services.AddAuthorization(options => options.AddPolicy(...))`.)

**Applying policies:**

```csharp
[Authorize(Policy = "SalesStaff")]
public IActionResult SalesDashboard() => View();

app.MapGet("/sales", () => "ok").RequireAuthorization("SalesStaff");
```

**Custom requirements.** When a rule needs real logic (like a minimum age), write a requirement and a handler.

```csharp
using Microsoft.AspNetCore.Authorization;

public class MinimumAgeRequirement : IAuthorizationRequirement
{
    public int MinimumAge { get; }
    public MinimumAgeRequirement(int minimumAge) => MinimumAge = minimumAge;
}

public class MinimumAgeHandler : AuthorizationHandler<MinimumAgeRequirement>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context, MinimumAgeRequirement requirement)
    {
        var birthClaim = context.User.FindFirst("BirthDate");
        if (birthClaim is null) return Task.CompletedTask;          // not succeeded → fails

        var birthDate = DateTime.Parse(birthClaim.Value);
        var age = DateTime.Today.Year - birthDate.Year;
        if (birthDate.Date > DateTime.Today.AddYears(-age)) age--;

        if (age >= requirement.MinimumAge)
            context.Succeed(requirement);                           // mark requirement as met

        return Task.CompletedTask;
    }
}

// Registration
builder.Services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();
builder.Services.AddAuthorizationBuilder()
    .AddPolicy("Over18", p => p.AddRequirements(new MinimumAgeRequirement(18)));
```

How evaluation works:

- A policy succeeds only if **all** its requirements succeed.
- A requirement succeeds if **at least one** of its handlers calls `Succeed`.
- A handler that does nothing (returns without `Succeed`) means "not satisfied by me". Call `context.Fail()` only to force failure regardless of other handlers.

**Default and fallback policies:**

```csharp
builder.Services.AddAuthorizationBuilder()
    .SetFallbackPolicy(new AuthorizationPolicyBuilder()
        .RequireAuthenticatedUser().Build());      // everything requires login unless [AllowAnonymous]
```

The **fallback policy** applies to endpoints with no authorization metadata. It is a "secure by default" setting.

---

## 16.4 Resource-based checks

Sometimes a decision depends on the **specific resource**: "a user can edit a document only if they own it". Attributes can't do this because the resource isn't loaded when the attribute runs. Instead, load the resource, then call **`IAuthorizationService`** imperatively.

**1. A requirement and handler that look at the resource:**

```csharp
public class Document
{
    public int Id { get; set; }
    public string OwnerId { get; set; } = "";
    public string Title { get; set; } = "";
}

public class SameOwnerRequirement : IAuthorizationRequirement { }

public class DocumentOwnerHandler : AuthorizationHandler<SameOwnerRequirement, Document>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context, SameOwnerRequirement requirement, Document resource)
    {
        var userId = context.User.FindFirstValue(ClaimTypes.NameIdentifier);
        if (userId is not null && resource.OwnerId == userId)
            context.Succeed(requirement);
        return Task.CompletedTask;
    }
}

builder.Services.AddSingleton<IAuthorizationHandler, DocumentOwnerHandler>();
builder.Services.AddAuthorizationBuilder()
    .AddPolicy("EditDocument", p => p.AddRequirements(new SameOwnerRequirement()));
```

**2. Check it in the action after loading the resource:**

```csharp
[Authorize]
[HttpPut("{id}")]
public async Task<IActionResult> Edit(int id, DocumentUpdate update,
                                      [FromServices] IAuthorizationService auth,
                                      [FromServices] AppDbContext db)
{
    var doc = await db.Documents.FindAsync(id);
    if (doc is null) return NotFound();

    var result = await auth.AuthorizeAsync(User, doc, "EditDocument");
    if (!result.Succeeded) return Forbid();            // 403 (or Challenge() if not logged in)

    doc.Title = update.Title;
    await db.SaveChangesAsync();
    return NoContent();
}

public record DocumentUpdate(string Title);
```

**Operation-based variant.** For several operations on one resource type (Read, Update, Delete), use `OperationAuthorizationRequirement`:

```csharp
public static class Operations
{
    public static readonly OperationAuthorizationRequirement Read   = new() { Name = nameof(Read) };
    public static readonly OperationAuthorizationRequirement Update = new() { Name = nameof(Update) };
    public static readonly OperationAuthorizationRequirement Delete = new() { Name = nameof(Delete) };
}

public class DocumentOperationHandler
    : AuthorizationHandler<OperationAuthorizationRequirement, Document>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context, OperationAuthorizationRequirement requirement, Document doc)
    {
        var userId = context.User.FindFirstValue(ClaimTypes.NameIdentifier);

        if (requirement == Operations.Read) context.Succeed(requirement);                 // anyone logged in
        else if (doc.OwnerId == userId)     context.Succeed(requirement);                 // owner: update/delete
        else if (context.User.IsInRole("Admin") && requirement == Operations.Delete)
            context.Succeed(requirement);

        return Task.CompletedTask;
    }
}

// Use: await auth.AuthorizeAsync(User, doc, Operations.Delete);
```

**Choosing an approach:**

| Need | Use |
|---|---|
| "Must be logged in" | `[Authorize]` |
| Simple groups | Roles |
| Based on user attributes | Claims-based policies |
| Custom logic about the user | Custom requirement + handler |
| Depends on a specific record (ownership) | Resource-based with `IAuthorizationService` |

**Return codes recap:** not logged in → `Challenge()` (401/redirect); logged in but not permitted → `Forbid()` (403). When you want to hide the existence of a resource, return `NotFound()` instead.

---

[Previous](./[15]-Authentication-And-Identity.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[17]-JWT-And-API-Security.md)
