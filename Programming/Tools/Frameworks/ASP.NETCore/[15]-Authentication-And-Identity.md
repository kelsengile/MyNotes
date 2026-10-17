[Previous](./[14]-Configuration-And-Logging.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[16]-Authorization.md)

*Security*

# Lesson 15 - Authentication And Identity

## 15.1 ASP.NET Core Identity

**Authentication** answers *"Who are you?"* (logging in). **Authorization** answers *"What are you allowed to do?"* (Lesson 16).

**ASP.NET Core Identity** is the built-in membership system. It provides:

- a user store (tables for users, roles, claims, logins, tokens),
- secure password hashing and validation,
- account lockout, email confirmation, two-factor authentication,
- external login support,
- ready-made UI (Razor Pages) and API endpoints.

**Packages:**

```bash
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

**1. The user class and DbContext:**

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore;

public class ApplicationUser : IdentityUser
{
    public string? DisplayName { get; set; }       // add your own fields
}

public class AppDbContext : IdentityDbContext<ApplicationUser>
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
}
```

`IdentityUser` already contains `Id`, `UserName`, `Email`, `PasswordHash`, `PhoneNumber`, `LockoutEnd`, and more. `IdentityDbContext` defines the Identity tables (`AspNetUsers`, `AspNetRoles`, `AspNetUserClaims`, ...). Create and apply a migration (Lesson 12) after adding it.

**2. Register Identity:**

```csharp
builder.Services.AddDbContext<AppDbContext>(o =>
    o.UseSqlServer(builder.Configuration.GetConnectionString("Default")));

builder.Services
    .AddIdentity<ApplicationUser, IdentityRole>(options =>
    {
        options.SignIn.RequireConfirmedEmail = false;
        options.User.RequireUniqueEmail = true;
    })
    .AddEntityFrameworkStores<AppDbContext>()
    .AddDefaultTokenProviders();

var app = builder.Build();
app.UseAuthentication();
app.UseAuthorization();
```

`AddDefaultIdentity<TUser>()` is a shortcut that also adds the default UI and uses cookie auth (used by the "Individual Accounts" project template). `AddIdentity` gives you more control and does **not** include UI.

**3. Use `UserManager` and `SignInManager`:**

```csharp
public class AccountController : Controller
{
    private readonly UserManager<ApplicationUser> _users;
    private readonly SignInManager<ApplicationUser> _signIn;

    public AccountController(UserManager<ApplicationUser> users, SignInManager<ApplicationUser> signIn)
    {
        _users = users;
        _signIn = signIn;
    }

    [HttpPost]
    public async Task<IActionResult> Register(RegisterViewModel model)
    {
        if (!ModelState.IsValid) return View(model);

        var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
        var result = await _users.CreateAsync(user, model.Password);   // hashes the password

        if (result.Succeeded)
        {
            await _signIn.SignInAsync(user, isPersistent: false);
            return RedirectToAction("Index", "Home");
        }

        foreach (var error in result.Errors)
            ModelState.AddModelError("", error.Description);
        return View(model);
    }

    [HttpPost]
    public async Task<IActionResult> Login(LoginViewModel model)
    {
        var result = await _signIn.PasswordSignInAsync(
            model.Email, model.Password, model.RememberMe, lockoutOnFailure: true);

        if (result.Succeeded) return RedirectToAction("Index", "Home");
        if (result.IsLockedOut) return View("Lockout");

        ModelState.AddModelError("", "Invalid login attempt.");
        return View(model);
    }

    [HttpPost]
    public async Task<IActionResult> Logout()
    {
        await _signIn.SignOutAsync();
        return RedirectToAction("Index", "Home");
    }
}
```

**Identity API endpoints (.NET 8+).** For SPAs and mobile clients, Identity can expose ready-made JSON endpoints:

```csharp
builder.Services.AddAuthorization();
builder.Services.AddIdentityApiEndpoints<ApplicationUser>()
    .AddEntityFrameworkStores<AppDbContext>();

var app = builder.Build();
app.MapIdentityApi<ApplicationUser>();     // /register, /login, /refresh, /manage/info ...
```

---

## 15.2 Cookies and sessions

**Cookie authentication.** After a successful login, the server sends a **cookie** containing an encrypted, signed ticket with the user's identity. The browser returns the cookie on every request, and the cookie middleware rebuilds `HttpContext.User` from it. This is how Identity works in server-rendered apps.

**Using cookie authentication without Identity:**

```csharp
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.LoginPath = "/account/login";
        options.AccessDeniedPath = "/account/denied";
        options.ExpireTimeSpan = TimeSpan.FromHours(8);
        options.SlidingExpiration = true;
        options.Cookie.HttpOnly = true;                         // not readable by JavaScript
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always; // HTTPS only
        options.Cookie.SameSite = SameSiteMode.Lax;             // limits cross-site sending
    });

app.UseAuthentication();
app.UseAuthorization();
```

Signing a user in manually:

```csharp
var claims = new List<Claim>
{
    new(ClaimTypes.NameIdentifier, "42"),
    new(ClaimTypes.Name, "alice"),
    new(ClaimTypes.Role, "Admin")
};
var identity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);
var principal = new ClaimsPrincipal(identity);

await HttpContext.SignInAsync(CookieAuthenticationDefaults.AuthenticationScheme, principal,
    new AuthenticationProperties { IsPersistent = true });

await HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);
```

When an unauthenticated user hits a protected page, the cookie handler **redirects to `LoginPath`** (for APIs you typically return `401` instead; see Lesson 17).

**Cookie security checklist:** `HttpOnly` (blocks JavaScript reads), `Secure` (HTTPS only), `SameSite` (reduces CSRF), reasonable expiration, and antiforgery tokens on state-changing forms (added by the form tag helper).

**Sessions** are a different feature: they store **temporary per-user server-side state** (like a shopping cart ID). A small session-ID cookie links the browser to data kept on the server.

```csharp
builder.Services.AddDistributedMemoryCache();           // storage (use Redis/SQL for multiple servers)
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(20);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
});

app.UseSession();    // after UseRouting, before endpoints

app.MapGet("/visit", (HttpContext ctx) =>
{
    int count = ctx.Session.GetInt32("visits") ?? 0;
    ctx.Session.SetInt32("visits", ++count);
    return $"Visit number {count}";
});
```

| | Authentication cookie | Session |
|---|---|---|
| Purpose | Proves *who* you are | Stores temporary data |
| Stored | In the cookie itself (encrypted) | On the server; cookie holds only an ID |
| Lifetime | Login duration | Idle timeout |

Do not store sensitive data in sessions unless the store is secured, and do not use sessions as a replacement for a real database.

---

## 15.3 External logins

**External (social) login** lets users sign in with Google, Microsoft, GitHub, Facebook, and so on. The user authenticates with the provider, and your app receives their identity. It uses the **OAuth 2.0 / OpenID Connect** flow:

```
1. User clicks "Sign in with Google"
2. App redirects to Google with a client id
3. User signs in and consents at Google
4. Google redirects back to your app's callback path with a code
5. Your app exchanges the code for tokens and reads the user's identity
6. Your app signs the user in with its own cookie
```

**Set up with Google:**

1. Create OAuth credentials in the Google Cloud Console. Add an authorized redirect URI such as `https://localhost:5001/signin-google`.
2. Store the client id and secret in **user secrets**, never in source control:

```bash
dotnet user-secrets set "Authentication:Google:ClientId" "xxxx.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "xxxx"
```

3. Add the provider package and register it:

```bash
dotnet add package Microsoft.AspNetCore.Authentication.Google
```

```csharp
builder.Services.AddAuthentication()
    .AddGoogle(options =>
    {
        options.ClientId = builder.Configuration["Authentication:Google:ClientId"]!;
        options.ClientSecret = builder.Configuration["Authentication:Google:ClientSecret"]!;
    });
```

(With `AddIdentity`/`AddDefaultIdentity`, call `AddAuthentication().AddGoogle(...)` after it. Other packages exist: `...Authentication.MicrosoftAccount`, `...Facebook`, `...Twitter`; community packages cover GitHub and more.)

**Using external logins with Identity** in a controller:

```csharp
public IActionResult ExternalLogin(string provider, string? returnUrl = null)
{
    var redirectUrl = Url.Action(nameof(ExternalLoginCallback), new { returnUrl });
    var props = _signIn.ConfigureExternalAuthenticationProperties(provider, redirectUrl);
    return Challenge(props, provider);                  // sends the user to the provider
}

public async Task<IActionResult> ExternalLoginCallback(string? returnUrl = null)
{
    var info = await _signIn.GetExternalLoginInfoAsync();
    if (info is null) return RedirectToAction(nameof(Login));

    var result = await _signIn.ExternalLoginSignInAsync(info.LoginProvider, info.ProviderKey, isPersistent: false);
    if (result.Succeeded) return LocalRedirect(returnUrl ?? "/");

    // First time: create a local account linked to the external login
    var email = info.Principal.FindFirstValue(ClaimTypes.Email);
    var user = new ApplicationUser { UserName = email, Email = email };
    await _users.CreateAsync(user);
    await _users.AddLoginAsync(user, info);
    await _signIn.SignInAsync(user, isPersistent: false);
    return LocalRedirect(returnUrl ?? "/");
}
```

**Benefits:** users don't create another password, and you avoid storing credentials. **Considerations:** handle users without an email, decide how to link provider accounts to existing local accounts, and verify email-confirmed status before merging accounts.

---

## 15.4 Password handling

**Never store passwords in plain text, and never use reversible encryption or fast hashes (MD5, SHA-1, plain SHA-256).** Identity stores a **salted, slow hash** produced by `PasswordHasher<TUser>`, which by default uses **PBKDF2** with a random salt and a high iteration count. The stored value includes the algorithm info, salt, and hash, so settings can be upgraded over time (hashes are re-hashed automatically on login when parameters change).

```csharp
var hasher = new PasswordHasher<ApplicationUser>();
string hash = hasher.HashPassword(user, "MyP@ssw0rd!");
var result = hasher.VerifyHashedPassword(user, hash, "MyP@ssw0rd!");   // PasswordVerificationResult.Success
```

In practice you let `UserManager.CreateAsync(user, password)` and `SignInManager.PasswordSignInAsync` do this.

**Password policy** is configurable:

```csharp
builder.Services.Configure<IdentityOptions>(options =>
{
    options.Password.RequiredLength = 10;
    options.Password.RequireDigit = true;
    options.Password.RequireLowercase = true;
    options.Password.RequireUppercase = true;
    options.Password.RequireNonAlphanumeric = false;
    options.Password.RequiredUniqueChars = 4;

    // Lockout: slows down brute-force guessing
    options.Lockout.MaxFailedAccessAttempts = 5;
    options.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(15);
    options.Lockout.AllowedForNewUsers = true;
});
```

Longer passwords matter more than complex-character rules; current guidance (e.g. NIST) favors length and checking against breached-password lists over forced complexity.

**Related account features to implement properly:**

| Feature | How |
|---|---|
| **Email confirmation** | `GenerateEmailConfirmationTokenAsync`, send link, `ConfirmEmailAsync`; set `SignIn.RequireConfirmedAccount` |
| **Password reset** | `GeneratePasswordResetTokenAsync` → email link → `ResetPasswordAsync`; never reveal whether an email exists |
| **Change password** | `ChangePasswordAsync` (requires the current password) |
| **Two-factor authentication** | Authenticator apps (TOTP) via `GetAuthenticatorKeyAsync`, SMS/email codes, recovery codes |
| **Lockout** | `lockoutOnFailure: true` in `PasswordSignInAsync` |

Newer .NET versions also add **passkey (WebAuthn)** support to Identity, which allows passwordless sign-in. Check the current documentation for your version.

**Other rules:** always require HTTPS; return the same generic "invalid login" message for wrong username and wrong password; rate-limit login endpoints (Lesson 17); and never log passwords or tokens.

---

[Previous](./[14]-Configuration-And-Logging.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[16]-Authorization.md)
