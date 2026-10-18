[Previous](./[6]-Eloquent-ORM.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[8]-Seeding-And-Factories.md)

*Data*

# Lesson 7 - Eloquent Relationships

## 7.1 One-to-many and one-to-one

Relationships are defined as methods on models. They let you move between related tables as if they were properties.

**One-to-many:** a user has many posts; each post belongs to one user. The `posts` table has a `user_id` column (`$table->foreignId('user_id')->constrained();`).

```php
class User extends Model
{
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}

class Post extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

Usage:

```php
$user->posts;                     // collection of Post models
$user->posts()->where('published', true)->get(); // query the relationship
$post->user->name;                // the owning user

$user->posts()->create(['title' => 'Hi', 'body' => '...']); // sets user_id for you
$post->user()->associate($user);  // set a belongsTo
$post->save();
```

- `$user->posts` (no parentheses) loads results.
- `$user->posts()` (parentheses) returns a query builder you can keep chaining.

**One-to-one:** a user has one profile; the `profiles` table has a `user_id` column.

```php
// User
public function profile()
{
    return $this->hasOne(Profile::class);
}

// Profile
public function user()
{
    return $this->belongsTo(User::class);
}
```

The side that holds the foreign key uses `belongsTo`. The other side uses `hasOne` or `hasMany`.

---

## 7.2 Many-to-many

A post can have many tags and a tag can have many posts. This needs a **pivot table** named after both tables in alphabetical order, singular: `post_tag`.

```php
Schema::create('post_tag', function (Blueprint $table) {
    $table->id();
    $table->foreignId('post_id')->constrained()->cascadeOnDelete();
    $table->foreignId('tag_id')->constrained()->cascadeOnDelete();
    $table->timestamps();
});
```

```php
// Post
public function tags()
{
    return $this->belongsToMany(Tag::class)->withTimestamps();
}

// Tag
public function posts()
{
    return $this->belongsToMany(Post::class);
}
```

Managing the pivot table:

```php
$post->tags()->attach($tagId);                // add one
$post->tags()->attach([1, 2, 3]);             // add several
$post->tags()->detach($tagId);                // remove one
$post->tags()->sync([1, 2, 3]);               // make the set exactly these IDs
$post->tags()->toggle([1, 2]);                // add missing, remove existing
$post->tags()->syncWithoutDetaching([4]);     // add without removing others
```

Extra pivot columns:

```php
return $this->belongsToMany(Tag::class)->withPivot('weight');

foreach ($post->tags as $tag) {
    echo $tag->pivot->weight;
}
```

Other relationship types exist, such as `hasManyThrough` and polymorphic relations (`morphMany`, `morphToMany`), which let one model (like `Comment`) belong to several different models.

---

## 7.3 Eager loading

By default, relationships are **lazy loaded**: the query runs the first time you access the property. Eager loading fetches relationships up front with one extra query.

```php
// Without eager loading: 1 query for posts + 1 query per post for its user
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->user->name;
}

// With eager loading: 2 queries total
$posts = Post::with('user')->get();
foreach ($posts as $post) {
    echo $post->user->name;
}
```

More options:

```php
Post::with(['user', 'tags'])->get();                // several relations
Post::with('comments.user')->get();                 // nested relations
Post::with(['comments' => fn ($q) => $q->latest()->limit(5)])->get(); // constrained
$posts->load('tags');                               // eager load after the fact
Post::withCount('comments')->get();                 // adds comments_count
Post::has('comments')->get();                       // only posts with comments
Post::whereHas('comments', fn ($q) => $q->where('approved', true))->get();
```

Make eager loading the default for a model:

```php
protected $with = ['user'];
```

Use this only when the relation is nearly always needed.

---

## 7.4 Avoiding N+1 queries

The **N+1 problem** happens when you run one query to load N records, then N more queries to load a relationship for each one. With 100 posts that is 101 queries instead of 2, which slows pages dramatically.

Ways to find and prevent it:

1. **Block lazy loading in development.** In `AppServiceProvider::boot()`:

```php
use Illuminate\Database\Eloquent\Model;

public function boot(): void
{
    Model::preventLazyLoading(! app()->isProduction());
}
```

Laravel will now throw an exception whenever a relationship is lazy loaded, so you notice the issue immediately.

2. **Inspect the queries.** Use tools like Laravel Debugbar or Telescope, or log queries temporarily:

```php
\DB::listen(fn ($query) => logger($query->sql));
```

3. **Always `with(...)`** the relationships you will loop over in a view.

4. **Count with `withCount`** instead of loading a whole collection just to call `->count()`.

5. **Select only needed columns** for large lists: `Post::with('user:id,name')->select('id', 'title', 'user_id')->get();` (remember to include the foreign key).

[Previous](./[6]-Eloquent-ORM.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[8]-Seeding-And-Factories.md)