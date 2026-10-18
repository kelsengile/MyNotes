[Previous](./[18]-The-Artisan-Console.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[20]-Deployment.md)

*Quality And Delivery*

# Lesson 19 - Testing Laravel Applications

## 19.1 PHPUnit and Pest

Automated tests check that your code keeps working as you change it. Laravel is built with testing in mind and supports two frameworks:

- **PHPUnit** — the long-standing standard, class-based.
- **Pest** — a newer framework built on PHPUnit with a shorter, more readable syntax. New projects can choose it during installation.

Tests live in the `tests` folder:

- `tests/Feature` — larger tests that exercise several parts together, such as an HTTP request, the database, and the response.
- `tests/Unit` — small tests of one class or function in isolation.

Create and run tests:

```bash
php artisan make:test PostTest                 # feature test
php artisan make:test PostCalculatorTest --unit
php artisan make:test PostTest --pest          # Pest style

php artisan test                               # run everything
php artisan test --filter=PostTest             # run matching tests
php artisan test --parallel                    # run in parallel
```

PHPUnit style:

```php
<?php

namespace Tests\Unit;

use PHPUnit\Framework\TestCase;

class MathTest extends TestCase
{
    public function test_addition(): void
    {
        $this->assertSame(4, 2 + 2);
    }
}
```

Pest style:

```php
test('addition works', function () {
    expect(2 + 2)->toBe(4);
});
```

Name tests so they describe behavior, for example `test_guests_cannot_create_posts`. Configuration lives in `phpunit.xml`, which already points tests at a separate environment.

---

## 19.2 Feature tests

A feature test simulates an HTTP request and checks the response.

```php
<?php

namespace Tests\Feature;

use App\Models\Post;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class PostTest extends TestCase
{
    use RefreshDatabase;

    public function test_posts_page_loads(): void
    {
        $response = $this->get('/posts');

        $response->assertStatus(200);
        $response->assertSee('All posts');
    }

    public function test_guest_cannot_create_a_post(): void
    {
        $this->post('/posts', ['title' => 'Hi', 'body' => 'Body'])
            ->assertRedirect('/login');
    }

    public function test_user_can_create_a_post(): void
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)->post('/posts', [
            'title' => 'My post',
            'body'  => 'Some content here.',
        ]);

        $response->assertRedirect('/posts');
        $this->assertDatabaseHas('posts', ['title' => 'My post']);
    }

    public function test_title_is_required(): void
    {
        $user = User::factory()->create();

        $this->actingAs($user)
            ->post('/posts', ['body' => 'No title'])
            ->assertSessionHasErrors('title');
    }
}
```

Useful methods:

| Method | Purpose |
|---|---|
| `get`, `post`, `put`, `patch`, `delete` | Make a request |
| `getJson`, `postJson` | Make a JSON request |
| `actingAs($user)` | Authenticate as a user |
| `assertStatus(200)`, `assertOk()`, `assertNotFound()`, `assertForbidden()` | Check status |
| `assertRedirect($url)` | Check redirect |
| `assertSee`, `assertDontSee` | Check page text |
| `assertViewHas('key')` | Check view data |
| `assertSessionHasErrors('field')` | Check validation errors |
| `assertJson([...])`, `assertJsonStructure([...])` | Check JSON |

API test example:

```php
$this->actingAs($user, 'sanctum')
    ->getJson('/api/posts')
    ->assertOk()
    ->assertJsonStructure(['data' => [['id', 'title']]]);
```

---

## 19.3 Database testing

Tests must not use your real data. Laravel uses a separate environment in tests (`APP_ENV=testing`). In `phpunit.xml` you can switch to a fast in-memory SQLite database:

```xml
<env name="DB_CONNECTION" value="sqlite"/>
<env name="DB_DATABASE" value=":memory:"/>
```

**Resetting the database between tests:**

```php
use Illuminate\Foundation\Testing\RefreshDatabase;

class PostTest extends TestCase
{
    use RefreshDatabase;
}
```

- `RefreshDatabase` runs migrations once, then wraps each test in a transaction that is rolled back, so every test starts clean.
- `LazilyRefreshDatabase` only migrates when a test actually touches the database.
- In Pest, enable it with `uses(RefreshDatabase::class)->in('Feature');` in `tests/Pest.php`.

**Creating test data with factories:**

```php
$user  = User::factory()->create();
$posts = Post::factory()->count(3)->for($user)->create();
```

**Database assertions:**

```php
$this->assertDatabaseHas('posts', ['title' => 'My post']);
$this->assertDatabaseMissing('posts', ['id' => $post->id]);
$this->assertDatabaseCount('posts', 3);
$this->assertSoftDeleted($post);
$this->assertModelExists($post);
$this->assertModelMissing($post);
```

Tips: keep each test independent, create only the data the test needs, and avoid depending on test order.

---

## 19.4 Mocking

Mocking replaces real behavior with a controlled stand-in, so tests do not send real emails, call real APIs, or write real files.

**Built-in fakes:**

```php
use Illuminate\Support\Facades\{Mail, Queue, Event, Notification, Storage, Http};

Mail::fake();
// ... run code that sends mail
Mail::assertSent(OrderShipped::class);
Mail::assertNothingSent();

Queue::fake();
ProcessPodcast::dispatch($podcast);
Queue::assertPushed(ProcessPodcast::class);

Event::fake();
Event::assertDispatched(OrderShipped::class);

Notification::fake();
Notification::assertSentTo($user, InvoicePaid::class);

Storage::fake('public');
$this->post('/avatar', ['avatar' => UploadedFile::fake()->image('a.jpg')]);
Storage::disk('public')->assertExists('avatars/...');
```

**Faking HTTP calls to external services:**

```php
Http::fake([
    'api.example.com/*' => Http::response(['rate' => 1.5], 200),
]);

// Code under test calling Http::get('https://api.example.com/rates') now gets the fake response
Http::assertSent(fn ($request) => $request->url() === 'https://api.example.com/rates');
```

**Mocking your own classes** with the container:

```php
$this->mock(PaymentGateway::class, function ($mock) {
    $mock->shouldReceive('charge')->once()->with(1000)->andReturn(true);
});
```

Because the controller asks for `PaymentGateway` through dependency injection, it receives the mock automatically (Lesson 13).

Other helpers: `Carbon::setTestNow('2030-01-01')` to freeze time (or `$this->travel(5)->days()`), and `$this->withoutExceptionHandling()` to see the real exception when debugging a failing test.

Mock only things you do not own or that are slow and unpredictable. Over-mocking can make tests pass even when the real code is broken.

[Previous](./[18]-The-Artisan-Console.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[20]-Deployment.md)