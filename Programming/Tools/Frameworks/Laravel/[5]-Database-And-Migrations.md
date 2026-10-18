[Previous](./[4]-Blade-Templates.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[6]-Eloquent-ORM.md)

*Data*

# Lesson 5 - Database And Migrations

## 5.1 Configuring connections

Database settings live in the `.env` file and are read by `config/database.php`.

**SQLite (default in new projects):**

```env
DB_CONNECTION=sqlite
```

The database file is `database/database.sqlite`.

**MySQL example:**

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=my_app
DB_USERNAME=root
DB_PASSWORD=secret
```

Supported databases include SQLite, MySQL, MariaDB, PostgreSQL, and SQL Server.

After changing `.env` values, clear the cached configuration if you have cached it:

```bash
php artisan config:clear
```

Check your connection:

```bash
php artisan db:show
```

Laravel also provides a **query builder** for writing database queries without raw SQL:

```php
use Illuminate\Support\Facades\DB;

$users = DB::table('users')->where('active', true)->orderBy('name')->get();
DB::table('users')->insert(['name' => 'Ada', 'email' => 'ada@example.com']);
```

Eloquent (Lesson 6) is built on top of this builder. Both use PDO prepared statements, which protect against SQL injection.

---

## 5.2 Writing migrations

A **migration** is a PHP file that describes a database change. Migrations act as version control for your schema: everyone on the team can run the same files to get the same tables.

Create one:

```bash
php artisan make:migration create_posts_table
php artisan make:model Post -m     # creates a model and a migration together
```

Files go in `database/migrations` with a timestamp prefix so they run in order.

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body');
            $table->boolean('published')->default(false);
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

- `up()` applies the change.
- `down()` reverses it.

Run all pending migrations:

```bash
php artisan migrate
```

Laravel records which migrations have run in a `migrations` table, so each runs only once.

---

## 5.3 Schema builder

**Common column types:**

| Method | Result |
|---|---|
| `$table->id()` | Auto-incrementing big integer primary key |
| `$table->string('name', 100)` | VARCHAR |
| `$table->text('body')` | TEXT |
| `$table->integer('votes')` | INTEGER |
| `$table->decimal('price', 8, 2)` | DECIMAL |
| `$table->boolean('active')` | BOOLEAN |
| `$table->date('born_on')` / `dateTime` / `timestamp` | Date and time types |
| `$table->json('options')` | JSON |
| `$table->enum('status', ['draft', 'live'])` | ENUM |
| `$table->timestamps()` | `created_at` and `updated_at` |
| `$table->softDeletes()` | Nullable `deleted_at` |

**Modifiers** chain onto a column:

```php
$table->string('email')->unique();
$table->string('nickname')->nullable();
$table->integer('stock')->default(0);
$table->string('slug')->index();
$table->text('notes')->nullable()->comment('Internal only');
```

**Foreign keys:**

```php
$table->foreignId('user_id')->constrained()->cascadeOnDelete();
```

`constrained()` guesses the `users` table from the column name. `cascadeOnDelete()` deletes a user's posts when the user is deleted.

**Changing an existing table** needs a new migration:

```bash
php artisan make:migration add_slug_to_posts_table --table=posts
```

```php
public function up(): void
{
    Schema::table('posts', function (Blueprint $table) {
        $table->string('slug')->nullable()->after('title');
    });
}

public function down(): void
{
    Schema::table('posts', function (Blueprint $table) {
        $table->dropColumn('slug');
    });
}
```

Never edit a migration that has already run in a shared environment. Write a new one instead.

---

## 5.4 Rolling back

| Command | What it does |
|---|---|
| `php artisan migrate:status` | Lists which migrations have run |
| `php artisan migrate:rollback` | Undoes the last batch of migrations |
| `php artisan migrate:rollback --step=2` | Undoes the last 2 migrations |
| `php artisan migrate:reset` | Rolls back all migrations |
| `php artisan migrate:refresh` | Rolls back everything and re-runs all migrations |
| `php artisan migrate:fresh` | Drops all tables, then runs all migrations |
| `php artisan migrate:fresh --seed` | Same as above, then runs seeders |

The difference between `refresh` and `fresh`: `refresh` calls each migration's `down()` method, while `fresh` simply drops every table and is faster and more reliable if a `down()` method is broken.

**Warning:** these commands destroy data. Never run `migrate:fresh` on a production database. In production, Laravel asks for confirmation before running destructive migration commands; use `--force` only if you are certain.

A **batch** is the group of migrations that ran during a single `php artisan migrate` call. `migrate:rollback` undoes one batch at a time.

[Previous](./[4]-Blade-Templates.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[6]-Eloquent-ORM.md)