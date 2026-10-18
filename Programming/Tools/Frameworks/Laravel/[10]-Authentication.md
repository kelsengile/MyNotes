[Previous](./[9]-Validation-And-Form-Requests.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[11]-Authorization.md)

*Application Features*

# Lesson 10 - Authentication

## 10.1 Starter kits like Breeze

**Authentication** answers "who are you?". Writing registration, login, logout, password reset, and email verification by hand is slow and risky, so Laravel offers **starter kits** that generate them for you.

**Laravel Breeze** is a minimal kit with simple, readable code. Install it in an existing project:

```bash
composer require laravel/breeze --dev
php artisan breeze:install
php artisan migrate
npm install && npm run dev
```

The installer asks which stack you want (Blade, Livewire, React or Vue with Inertia, or an API). It then adds routes, controllers, views, and tests into your own code, so you can edit everything.

Other options:

- **Jetstream** — a larger kit with teams and two-factor authentication.
- **Fortify** — headless backend only (no views).
- **Newer official starter kits** — recent versions of the installer (`laravel new`) offer official React, Vue, and Livewire kits. Check the current Laravel documentation to see which kits your version provides.

Pick one kit per project. Read the generated code, since it is the best way to learn how authentication works.

---

## 10.2 Sessions and guards

Laravel's authentication has two core ideas:

- **Guards** define *how* users are authenticated for each request. The default `web` guard uses **sessions** and cookies; a `token`-style guard handles APIs.
- **Providers** define *where* users are retrieved from, usually the Eloquent `User` model.

Both are configured in `config/auth.php`.

**Logging in manually:**

```php
use Illuminate\Support\Facades\Auth;

public function login(Request $request)
{
    $credentials = $request->validate([
        'email' => ['required', 'email'],
        'password' => ['required'],
    ]);

    if (Auth::attempt($credentials, $request->boolean('remember'))) {
        $request->session()->regenerate(); // prevents session fixation
        return redirect()->intended('dashboard');
    }

    return back()->withErrors([
        'email' => 'The provided credentials do not match our records.',
    ])->onlyInput('email');
}

public function logout(Request $request)
{
    Auth::logout();
    $request->session()->invalidate();
    $request->session()->regenerateToken();
    return redirect('/');
}
```

`Auth::attempt()` finds the user by email, then compares the password hash. Passwords are always stored hashed (`'password' => 'hashed'` cast or `Hash::make()`), never as plain text.

**Reading the logged-in user:**

```php
Auth::user();       // the user model or null
Auth::id();         // the ID or null
Auth::check();      // true if logged in
$request->user();   // same user, from the request
```

**Protecting routes:**

```php
Route::get('/dashboard', DashboardController::class)->middleware('auth');
Route::get('/login', [LoginController::class, 'create'])->middleware('guest');
```

---

## 10.3 Password reset and verification

**Email verification.** Make the user model implement `MustVerifyEmail`:

```php
use Illuminate\Contracts\Auth\MustVerifyEmail;

class User extends Authenticatable implements MustVerifyEmail
{
    // ...
}
```

Then require verification with the `verified` middleware:

```php
Route::get('/billing', BillingController::class)->middleware(['auth', 'verified']);
```

When a user registers, Laravel emails a signed verification link. Starter kits already include the routes and views for verification and for resending the email.

**Password reset.** The flow is:

1. The user submits their email on a "forgot password" form.
2. Laravel creates a token (stored in the `password_reset_tokens` table) and emails a reset link.
3. The user opens the link, enters a new password, and the token is validated.

Core code with the `Password` facade:

```php
use Illuminate\Support\Facades\Password;

$status = Password::sendResetLink($request->only('email'));

$status = Password::reset(
    $request->only('email', 'password', 'password_confirmation', 'token'),
    function ($user, $password) {
        $user->forceFill(['password' => $password])->save();
    }
);
```

Both flows send mail, so configure your mail settings (Lesson 15). During development set `MAIL_MAILER=log` to write emails to `storage/logs/laravel.log`.

---

## 10.4 Laravel Sanctum

**Sanctum** provides lightweight authentication for two cases:

1. **API tokens** for mobile apps and third-party clients.
2. **Cookie-based authentication for SPAs** (a JavaScript front end on the same top-level domain).

Install the API scaffolding, which includes Sanctum, in a new project:

```bash
php artisan install:api
```

Add the trait to your user model:

```php
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens;
}
```

**Issuing a token:**

```php
$token = $user->createToken('mobile-app')->plainTextToken;
```

Show `plainTextToken` to the client once. Only a hash is stored in the database.

**Protecting routes:**

```php
Route::middleware('auth:sanctum')->get('/user', fn (Request $request) => $request->user());
```

**Calling the API:**

```
GET /api/user
Authorization: Bearer <token>
Accept: application/json
```

**Abilities** limit what a token can do:

```php
$token = $user->createToken('reports', ['reports:read'])->plainTextToken;

if ($request->user()->tokenCan('reports:read')) { /* ... */ }
```

**Revoking tokens:**

```php
$request->user()->currentAccessToken()->delete(); // this token
$request->user()->tokens()->delete();             // all tokens
```

[Previous](./[9]-Validation-And-Form-Requests.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[11]-Authorization.md)