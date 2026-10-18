[Previous](./[5]-Database-And-Migrations.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[7]-Eloquent-Relationships.md)

*Data*

# Lesson 6 - Eloquent ORM

## 6.1 Defining models

**Eloquent** is Laravel's ORM (Object-Relational Mapper). Each database table has a matching **model** class, and each row becomes an object.

```bash
php artisan make:model Post
```

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    protected $fillable = ['title', 'body', 'published'];

    protected function casts(): array
    {
        return [
            'published' => 'boolean',
            'published_at' => 'datetime',
        ];
    }
}
```

**Conventions** (you can override them if needed):

- Table name: plural snake_case of the class (`Post` → `posts`, `BlogPost` → `blog_posts`).
- Primary key: `id`.
- Timestamps: `created_at` and `updated_at` are managed automatically.

Override when necessary:

```php
protected $table = 'my_posts';
protected $primaryKey = 'post_id';
public $timestamps = false;
```

**Mass assignment protection:** `$fillable` lists attributes that may be set in bulk (`Post::create([...])`). This stops users from slipping in fields like `is_admin`. The opposite is `$guarded`, which lists protected fields. Use one or the other.

**Casts** convert database values to PHP types automatically (booleans, dates, arrays, enums, encrypted values).

---

## 6.2 CRUD operations

**Create:**

```php
$post = Post::create(['title' => 'Hello', 'body' => 'First post']);

$post = new Post();
$post->title = 'Hello';
$post->body = 'First post';
$post->save();
```

**Read:**

```php
Post::all();                              // every row (careful with large tables)
Post::find(1);                            // by primary key, or null
Post::findOrFail(1);                      // or throws a 404 in web requests
Post::where('published', true)->get();    // collection of results
Post::where('title', 'Hello')->first();   // first match or null
Post::orderBy('created_at', 'desc')->take(5)->get();
Post::latest()->paginate(15);             // pagination
Post::count();
Post::where('views', '>', 100)->exists();
```

**Update:**

```php
$post = Post::find(1);
$post->title = 'New title';
$post->save();

$post->update(['title' => 'New title']);               // uses $fillable
Post::where('published', false)->update(['published' => true]); // bulk update
```

**Delete:**

```php
$post->delete();
Post::destroy(1);
Post::destroy([1, 2, 3]);
```

**Helpers:**

```php
Post::firstOrCreate(['slug' => 'hello'], ['title' => 'Hello']); // find or create
Post::updateOrCreate(['slug' => 'hello'], ['title' => 'Updated']);
```

**Soft deletes** keep the row but mark it as deleted. Add `$table->softDeletes()` in the migration and `use SoftDeletes;` in the model (`use Illuminate\Database\Eloquent\SoftDeletes;`). Then `delete()` fills `deleted_at`, `withTrashed()` includes deleted rows, and `restore()` brings one back.

---

## 6.3 Query scopes

Scopes package reusable query conditions.

**Local scope** (applied only when you call it). The attribute style is supported in recent Laravel versions:

```php
use Illuminate\Database\Eloquent\Attributes\Scope;
use Illuminate\Database\Eloquent\Builder;

class Post extends Model
{
    #[Scope]
    protected function published(Builder $query): void
    {
        $query->where('published', true);
    }
}
```

The older naming convention also still works in all versions:

```php
public function scopePublished(Builder $query): void
{
    $query->where('published', true);
}
```

Use either one the same way:

```php
$posts = Post::published()->latest()->get();
```

Scopes can take arguments:

```php
public function scopeOfAuthor(Builder $query, int $userId): void
{
    $query->where('user_id', $userId);
}

Post::ofAuthor(3)->get();
```

**Global scopes** are applied to every query for a model automatically (soft deletes use one). Use them sparingly because they can surprise other developers.

---

## 6.4 Accessors and mutators

An **accessor** changes how an attribute is read. A **mutator** changes how it is stored. Both use `Illuminate\Database\Eloquent\Casts\Attribute`.

```php
use Illuminate\Database\Eloquent\Casts\Attribute;

class User extends Model
{
    // Accessor and mutator for "name"
    protected function name(): Attribute
    {
        return Attribute::make(
            get: fn (string $value) => ucfirst($value),
            set: fn (string $value) => strtolower($value),
        );
    }

    // Computed attribute with no database column
    protected function fullName(): Attribute
    {
        return Attribute::make(
            get: fn () => $this->first_name . ' ' . $this->last_name,
        );
    }
}
```

Usage:

```php
$user->name = 'ADA';       // stored as "ada"
echo $user->name;          // "Ada"
echo $user->full_name;     // accessed in snake_case
```

Method name in camelCase (`fullName`) maps to attribute name in snake_case (`full_name`).

For simple type conversions, prefer **casts** over accessors. For hashing, the `'hashed'` cast does it for you:

```php
protected function casts(): array
{
    return ['password' => 'hashed'];
}
```

[Previous](./[5]-Database-And-Migrations.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[7]-Eloquent-Relationships.md)