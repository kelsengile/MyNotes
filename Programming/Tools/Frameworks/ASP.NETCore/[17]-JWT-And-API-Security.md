[Previous](./[16]-Authorization.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[18]-Web-API-Design.md)

*Security*

# Lesson 17 - JWT And API Security

## 17.1 JWT bearer authentication

Cookie authentication suits server-rendered sites. For **APIs** consumed by SPAs, mobile apps, or other services, **token-based authentication** is more common. The client sends a token in the `Authorization` header on each request:

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

A **JSON Web Token (JWT)** is a compact, **signed** token with three Base64URL parts separated by dots: `header.payload.signature`.

- **Header:** algorithm (e.g. `HS256`) and type.
- **Payload:** claims (`sub`, `name`, `role`, `exp` expiry, `iss` issuer, `aud` audience).
- **Signature:** proves the token was issued by someone holding the key and wasn't altered.

JWTs are **signed, not encrypted**: anyone can read the payload. **Never put secrets in a JWT.** The server verifies the signature and expiry without storing session state (it is *stateless*).

**Flow:**

```
1. Client POSTs credentials to /login
2. Server validates them and returns a signed JWT (access token)
3. Client stores it and sends it with every API request
4. Server validates signature, issuer, audience, expiry → sets HttpContext.User
```

**Install the package:**

```bash
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

**Settings** (key from user secrets or environment variables, **never** committed):

```json
{
  "Jwt": {
    "Issuer": "https://myapi.example.com",
    "Audience": "myapi-clients",
    "ExpiryMinutes": 30
  }
}
```

```bash
dotnet user-secrets set "Jwt:Key" "a-long-random-secret-of-at-least-32-characters!!"
```

**Configure validation:**

```csharp
using System.Text;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;

var key = Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]!);

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidateAudience = true,
            ValidAudience = builder.Configuration["Jwt:Audience"],
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(key),
            ValidateLifetime = true,
            ClockSkew = TimeSpan.FromSeconds(30)
        };
    });
builder.Services.AddAuthorization();

app.UseAuthentication();
app.UseAuthorization();
```

**Issue a token** from a login endpoint:

```csharp
using System.Security.Claims;
using Microsoft.IdentityModel.JsonWebTokens;

app.MapPost("/login", (LoginRequest login, IConfiguration config) =>
{
    // Replace with real credential validation (e.g. Identity's SignInManager / UserManager)
    if (login.Username != "alice" || login.Password != "P@ssw0rd!")
        return Results.Unauthorized();

    var descriptor = new SecurityTokenDescriptor
    {
        Subject = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.NameIdentifier, "42"),
            new Claim(ClaimTypes.Name, login.Username),
            new Claim(ClaimTypes.Role, "Admin")
        }),
        Issuer = config["Jwt:Issuer"],
        Audience = config["Jwt:Audience"],
        Expires = DateTime.UtcNow.AddMinutes(config.GetValue<int>("Jwt:ExpiryMinutes", 30)),
        SigningCredentials = new SigningCredentials(
            new SymmetricSecurityKey(Encoding.UTF8.GetBytes(config["Jwt:Key"]!)),
            SecurityAlgorithms.HmacSha256)
    };

    string token = new JsonWebTokenHandler().CreateToken(descriptor);
    return Results.Ok(new { access_token = token });
});

app.MapGet("/secure", (ClaimsPrincipal user) => $"Hello {user.Identity?.Name}")
   .RequireAuthorization();

public record LoginRequest(string Username, string Password);
```

**Call it:**

```bash
curl -X POST https://localhost:5001/login -H "Content-Type: application/json" \
     -d '{"username":"alice","password":"P@ssw0rd!"}'
curl https://localhost:5001/secure -H "Authorization: Bearer <token>"
```

**Best practices:**

- Keep **access tokens short-lived** (5-30 minutes). Use **refresh tokens** (long-lived, stored securely and revocable on the server) to get new access tokens.
- Use a strong key (at least 256 bits for HS256); for multiple services consider asymmetric keys (RS256/ES256) or an identity provider (Microsoft Entra ID, Auth0, Keycloak, Duende IdentityServer).
- Always validate issuer, audience, lifetime and signature.
- Store tokens in browsers carefully: `localStorage` is exposed to XSS; `HttpOnly` cookies are safer but need CSRF protection.
- You cannot "log out" a JWT before it expires unless you track revoked tokens, which is why short lifetimes matter.

---

## 17.2 CORS

**CORS (Cross-Origin Resource Sharing)** is a **browser** security rule. By default, JavaScript on `https://app.example.com` cannot read responses from `https://api.example.com` (a different **origin**: scheme + host + port) unless the API explicitly allows it with CORS response headers.

Key points:

- CORS is enforced by **browsers only**. It doesn't stop `curl`, Postman, or other servers. It is **not** a replacement for authentication.
- For non-simple requests (e.g. JSON `POST`, custom headers), the browser first sends a **preflight** `OPTIONS` request; the server must answer with the allowed methods/headers.

**Configure a policy:**

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("WebApp", policy => policy
        .WithOrigins("https://app.example.com", "http://localhost:3000")
        .WithMethods("GET", "POST", "PUT", "DELETE")
        .WithHeaders("Content-Type", "Authorization")
        .AllowCredentials());                       // needed for cookies; cannot be combined with AllowAnyOrigin
});

var app = builder.Build();

app.UseRouting();
app.UseCors("WebApp");                               // after UseRouting, before UseAuthentication/Authorization
app.UseAuthentication();
app.UseAuthorization();
```

**Apply per endpoint instead of globally:**

```csharp
app.MapGet("/public-data", () => "hi").RequireCors("WebApp");

[EnableCors("WebApp")]
public class ProductsController : ControllerBase { }

[DisableCors]
public IActionResult Internal() => Ok();
```

**Avoid `AllowAnyOrigin()` in production** (it is acceptable for truly public, credential-free APIs). Typical errors:

| Browser error | Cause |
|---|---|
| *No 'Access-Control-Allow-Origin' header* | Origin not allowed, or `UseCors` is missing / in the wrong place |
| *Preflight request doesn't pass* | Method or header not allowed by the policy |
| *Wildcard with credentials not allowed* | Used `AllowAnyOrigin` with `AllowCredentials`; list explicit origins |

---

## 17.3 Rate limiting

**Rate limiting** protects your API from abuse, brute-force login attempts, and accidental overload by restricting how many requests a client may make. ASP.NET Core includes it (.NET 7+) via `Microsoft.AspNetCore.RateLimiting`.

**Algorithms:**

| Limiter | Behavior |
|---|---|
| **Fixed window** | N requests per time window (e.g. 100 per minute); resets at window end |
| **Sliding window** | Like fixed, but the window slides in segments, smoothing bursts |
| **Token bucket** | Tokens refill at a steady rate; each request spends one; allows controlled bursts |
| **Concurrency** | Limits the number of requests being processed at once |

**Configure:**

```csharp
using System.Threading.RateLimiting;
using Microsoft.AspNetCore.RateLimiting;

builder.Services.AddRateLimiter(options =>
{
    options.RejectionStatusCode = StatusCodes.Status429TooManyRequests;

    // A named policy for general API use
    options.AddFixedWindowLimiter("api", o =>
    {
        o.PermitLimit = 100;
        o.Window = TimeSpan.FromMinutes(1);
        o.QueueLimit = 0;                              // reject immediately when over the limit
    });

    // A stricter policy for login
    options.AddFixedWindowLimiter("login", o =>
    {
        o.PermitLimit = 5;
        o.Window = TimeSpan.FromMinutes(1);
    });

    // A global per-client limiter, partitioned by IP address
    options.GlobalLimiter = PartitionedRateLimiter.Create<HttpContext, string>(ctx =>
        RateLimitPartition.GetFixedWindowLimiter(
            ctx.Connection.RemoteIpAddress?.ToString() ?? "unknown",
            _ => new FixedWindowRateLimiterOptions
            {
                PermitLimit = 200,
                Window = TimeSpan.FromMinutes(1)
            }));
});

var app = builder.Build();
app.UseRateLimiter();                                   // after UseRouting

app.MapPost("/login", () => "ok").RequireRateLimiting("login");
app.MapGroup("/api").RequireRateLimiting("api");
```

When the limit is exceeded the client receives **HTTP 429 Too Many Requests**. You can customize the rejection with `options.OnRejected` (for example, adding a `Retry-After` header).

**Notes:** behind a reverse proxy, the "client IP" is the proxy's unless you configure forwarded headers (see Lesson 21). The built-in limiter keeps counts **in memory per instance**; with several servers, use a gateway/shared-store solution for global limits.

---

## 17.4 HTTPS and data protection

**HTTPS** encrypts traffic between client and server, which is essential for logins, cookies, and tokens. Without it, credentials and tokens can be read or altered on the network.

**Enabling it:**

```csharp
if (!app.Environment.IsDevelopment())
{
    app.UseHsts();                // tells browsers: always use HTTPS for this host (default 30 days)
}
app.UseHttpsRedirection();        // redirects http:// requests to https://
```

**Local development certificate:**

```bash
dotnet dev-certs https --trust
```

In production, use a real certificate: terminate TLS at a reverse proxy/load balancer or cloud service (Azure App Service, nginx, etc.), or configure Kestrel with a certificate (e.g. from Let's Encrypt). Don't use the development certificate in production. Configure HSTS carefully because browsers cache it.

**The Data Protection API** is the system ASP.NET Core uses to **encrypt and sign** sensitive data: authentication cookies, antiforgery tokens, TempData, session cookies, and password-reset tokens. You can use it for your own data too:

```csharp
public class SecretService
{
    private readonly IDataProtector _protector;

    public SecretService(IDataProtectionProvider provider)
        => _protector = provider.CreateProtector("MyApp.SecretService.v1");   // purpose string

    public string Protect(string plain)   => _protector.Protect(plain);
    public string Unprotect(string token) => _protector.Unprotect(token);
}
```

Data protected with one *purpose* string can't be unprotected with another.

**Key management matters in production.** Data Protection keys encrypt your cookies and tokens. If keys are lost (e.g. in a container that restarts, or not shared between servers), **all existing cookies become invalid**, and multi-server setups break. Persist and share keys:

```csharp
builder.Services.AddDataProtection()
    .SetApplicationName("MyApp")                                            // same name on all instances
    .PersistKeysToFileSystem(new DirectoryInfo("/var/keys"));              // mounted volume
// Other options: PersistKeysToDbContext<AppDbContext>(), Azure Blob Storage, Redis,
// and protecting keys at rest with ProtectKeysWithCertificate(...) or Azure Key Vault.
```

**A security checklist for APIs:**

| Concern | Measure |
|---|---|
| Transport | HTTPS everywhere, HSTS |
| Identity | Strong password policy, lockout, MFA; short-lived JWTs |
| Access | Authorize every endpoint; fallback policy; resource-based checks |
| Input | Validate all input (Lesson 9); use parameterized queries (EF Core) |
| Abuse | Rate limiting; request size limits |
| Browsers | Correct CORS policy; antiforgery for cookie-based forms |
| Secrets | Never in source control; use secret stores (Lesson 14) |
| Errors | Return `ProblemDetails`; never expose stack traces in production |
| Dependencies | Keep .NET and NuGet packages updated |

---

[Previous](./[16]-Authorization.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[18]-Web-API-Design.md)
