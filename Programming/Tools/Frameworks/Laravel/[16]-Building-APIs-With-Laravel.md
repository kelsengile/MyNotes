[Previous](./[15]-Mail-Notifications-And-File-Storage.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[17]-Frontend-Stacks.md)

*APIs And Frontend*

# Lesson 16 - Building APIs With Laravel

## 16.1 API routes

An **API** returns data (usually JSON) instead of HTML pages, to be consumed by mobile apps, JavaScript front ends, or other services.

In Laravel 11 and newer, API support is installed on demand:

```bash
php artisan install:api
```

This creates `routes/api.php`, installs Sanctum, and runs the needed migration. Routes in this file get the `/api` prefix and the `api` middleware group automatically (stateless, rate limited, no sessions or CSRF).

```php
// routes/api.php
use App\Http\Controllers\Api\PostController;

Route::get('/ping', fn () => ['status' => 'ok']);   // GET /api/ping

Route::apiResource('posts', PostController::class); // GET/POST/PUT/DELETE /api/posts
```

Controller returning JSON:

```php
public function index()
{
    return Post::latest()->paginate(15);   // models and paginators become JSON
}

public function store(StorePostRequest $request)
{
    $post = Post::create($request->validated());

    return response()->json($post, 201);   // 201 Created
}

public function destroy(Post $post)
{
    $post->delete();

    return response()->noContent();        // 204 No Content
}
```

Use correct HTTP status codes: `200` OK, `201` created, `204` no content, `401` unauthenticated, `403` forbidden, `404` not found, `422` validation failed, `429` too many requests.

Clients should send `Accept: application/json` so errors (such as validation failures) come back as JSON instead of redirects.

---

## 16.2 API resources

Returning models directly exposes every column. **API resources** are a transformation layer that controls exactly what the JSON looks like.

```bash
php artisan make:resource PostResource
php artisan make:resource PostCollection   # optional, for custom collection wrapping
```

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class PostResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id'         => $this->id,
            'title'      => $this->title,
            'excerpt'    => str($this->body)->limit(100),
            'author'     => new UserResource($this->whenLoaded('user')),
            'tags'       => TagResource::collection($this->whenLoaded('tags')),
            'created_at' => $this->created_at->toIso8601String(),
        ];
    }
}
```

Use it:

```php
public function show(Post $post)
{
    return new PostResource($post->load('user'));
}

public function index()
{
    return PostResource::collection(Post::with('user')->paginate(15));
}
```

Key points:

- Results are wrapped in a `data` key by default. Paginated results also add `links` and `meta`.
- `whenLoaded('user')` includes the relation only if it was eager loaded, which avoids N+1 queries.
- Use `$this->when($condition, $value)` for conditional fields.
- Disable wrapping globally with `JsonResource::withoutWrapping()` in a service provider.

---

## 16.3 Token authentication

Use **Sanctum** (Lesson 10) to protect API routes with bearer tokens.

```php
// routes/api.php
Route::post('/login', function (Request $request) {
    $request->validate([
        'email' => ['required', 'email'],
        'password' => ['required'],
    ]);

    $user = User::where('email', $request->email)->first();

    if (! $user || ! Hash::check($request->password, $user->password)) {
        return response()->json(['message' => 'Invalid credentials'], 401);
    }

    return ['token' => $user->createToken('api')->plainTextToken];
});

Route::middleware('auth:sanctum')->group(function () {
    Route::get('/me', fn (Request $request) => $request->user());
    Route::apiResource('posts', PostController::class);
    Route::post('/logout', function (Request $request) {
        $request->user()->currentAccessToken()->delete();
        return response()->noContent();
    });
});
```

The client sends the token with each request:

```
Authorization: Bearer 1|abcdef...
Accept: application/json
```

Testing with curl:

```bash
curl -X POST http://localhost:8000/api/login \
  -H "Accept: application/json" \
  -d "email=ada@example.com" -d "password=secret"

curl http://localhost:8000/api/me \
  -H "Accept: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

Always use HTTPS in production so tokens are not sent in clear text. Give tokens abilities (`createToken('name', ['posts:write'])`) and check them with `tokenCan`.

---

## 16.4 Versioning and rate limits

**Versioning** lets you change an API without breaking existing clients. The simplest approach is a URL prefix:

```php
Route::prefix('v1')->group(function () {
    Route::apiResource('posts', \App\Http\Controllers\Api\V1\PostController::class);
});

Route::prefix('v2')->group(function () {
    Route::apiResource('posts', \App\Http\Controllers\Api\V2\PostController::class);
});
```

Final URLs: `/api/v1/posts` and `/api/v2/posts`. Keep resources and controllers in `V1` and `V2` namespaces so each version stays independent. Avoid breaking changes inside a version (renaming fields, changing types). Add new fields instead.

**Rate limits.** The `api` group uses a rate limiter named `api` if you define one (see Lesson 12). Customize per route group:

```php
Route::middleware(['auth:sanctum', 'throttle:100,1'])->group(function () {
    // 100 requests per minute
});
```

Exceeding the limit returns `429` and includes headers such as `X-RateLimit-Limit`, `X-RateLimit-Remaining`, and `Retry-After`.

**Consistent errors.** Customize exception output in `bootstrap/app.php` when needed:

```php
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->render(function (\Illuminate\Database\Eloquent\ModelNotFoundException $e, Request $request) {
        if ($request->is('api/*')) {
            return response()->json(['message' => 'Resource not found'], 404);
        }
    });
})
```

Document your API for consumers using tools such as OpenAPI/Swagger (for example via the Scribe or Scramble packages).

[Previous](./[15]-Mail-Notifications-And-File-Storage.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[17]-Frontend-Stacks.md)