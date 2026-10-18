[Previous](./[7]-Eloquent-Relationships.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[9]-Validation-And-Form-Requests.md)

*Data*

# Lesson 8 - Seeding And Factories

## 8.1 Seeders

A **seeder** is a class that inserts data into your database. Use seeders for starting data (roles, categories) or development data.

```bash
php artisan make:seeder CategorySeeder
```

```php
<?php

namespace Database\Seeders;

use App\Models\Category;
use Illuminate\Database\Seeder;

class CategorySeeder extends Seeder
{
    public function run(): void
    {
        foreach (['News', 'Tutorials', 'Opinion'] as $name) {
            Category::firstOrCreate(['name' => $name]);
        }
    }
}
```

`firstOrCreate` makes the seeder safe to run more than once.

Register it in `database/seeders/DatabaseSeeder.php`:

```php
public function run(): void
{
    $this->call([
        CategorySeeder::class,
        UserSeeder::class,
    ]);
}
```

Run seeders:

```bash
php artisan db:seed                         # runs DatabaseSeeder
php artisan db:seed --class=CategorySeeder  # one seeder
php artisan migrate:fresh --seed            # rebuild and seed in one step
```

Order matters: seed parent tables (categories) before tables that depend on them (posts).

---

## 8.2 Model factories

A **factory** describes how to generate fake model instances. It is ideal for tests and large amounts of development data.

```bash
php artisan make:factory PostFactory --model=Post
```

```php
<?php

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class PostFactory extends Factory
{
    public function definition(): array
    {
        return [
            'user_id'   => User::factory(),
            'title'     => fake()->sentence(),
            'body'      => fake()->paragraphs(3, true),
            'published' => false,
        ];
    }

    // A "state" is a named variation
    public function published(): static
    {
        return $this->state(fn (array $attributes) => [
            'published' => true,
        ]);
    }
}
```

Models need the `HasFactory` trait (generated models include it):

```php
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Post extends Model
{
    use HasFactory;
}
```

Usage:

```php
Post::factory()->create();                    // saved to the database
Post::factory()->make();                      // object only, NOT saved
Post::factory()->count(10)->create();
Post::factory()->published()->create();
Post::factory()->create(['title' => 'Custom']); // override attributes
```

`'user_id' => User::factory()` means a new user is created for each post unless you pass one in.

---

## 8.3 Faker data

Factories use the **Faker** library to generate realistic fake values. In Laravel, call it with the `fake()` helper.

| Call | Example output |
|---|---|
| `fake()->name()` | `Ada Lovelace` |
| `fake()->unique()->safeEmail()` | `ada@example.org` |
| `fake()->sentence()` | A random sentence |
| `fake()->paragraph()` | A random paragraph |
| `fake()->numberBetween(1, 100)` | `42` |
| `fake()->boolean(70)` | `true` about 70% of the time |
| `fake()->dateTimeBetween('-1 year')` | A date within the last year |
| `fake()->randomElement(['draft', 'live'])` | One item from the list |
| `fake()->address()` / `phoneNumber()` / `url()` / `company()` | More realistic details |

`unique()` guarantees no duplicates within the run, which matters for unique database columns.

Change the language of generated data in `.env`:

```env
APP_FAKER_LOCALE=en_US
```

Always make a factory's default state valid, meaning it should satisfy every database constraint so `create()` works without extra arguments.

---

## 8.4 Test data strategies

**Combining factories and relationships:**

```php
// A user with 5 posts
User::factory()->has(Post::factory()->count(5))->create();

// Same thing with the magic method
User::factory()->hasPosts(5)->create();

// Posts belonging to an existing user
Post::factory()->count(3)->for($user)->create();

// Many-to-many
Post::factory()->hasAttached(Tag::factory()->count(3))->create();
```

**Sequences** alternate values:

```php
use Illuminate\Database\Eloquent\Factories\Sequence;

User::factory()->count(4)->state(new Sequence(
    ['role' => 'admin'],
    ['role' => 'member'],
))->create();
```

**Good practices:**

- Use `create()` when the test needs database rows, and `make()` when a plain object is enough because it is faster.
- Keep seeders for stable reference data (roles, countries) and factories for volume or test data.
- Make seeders **idempotent** with `firstOrCreate` or `updateOrCreate` so running them twice does not duplicate rows.
- Do not seed default admin passwords in production. Create such accounts through a command or an environment-specific seeder.
- Use `DatabaseSeeder` to run extra development data only when `app()->isLocal()`:

```php
if (app()->isLocal()) {
    $this->call(DemoDataSeeder::class);
}
```

[Previous](./[7]-Eloquent-Relationships.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[9]-Validation-And-Form-Requests.md)