[Previous](./[17]-Frontend-Stacks.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[19]-Testing-Laravel-Applications.md)

*Quality And Delivery*

# Lesson 18 - The Artisan Console

## 18.1 Built-in commands

**Artisan** is Laravel's command-line interface. It lives in the `artisan` file at your project root and is run with `php artisan`.

```bash
php artisan list              # show all commands
php artisan help migrate      # help for one command
php artisan about             # overview of your app and environment
```

Frequently used commands:

| Command | Purpose |
|---|---|
| `php artisan serve` | Start the local development server |
| `php artisan migrate` | Run pending migrations |
| `php artisan db:seed` | Run seeders |
| `php artisan route:list` | List all routes |
| `php artisan config:clear` | Clear cached configuration |
| `php artisan cache:clear` | Clear the application cache |
| `php artisan view:clear` | Clear compiled Blade views |
| `php artisan optimize:clear` | Clear all cached files at once |
| `php artisan storage:link` | Link `public/storage` to `storage/app/public` |
| `php artisan queue:work` | Process queued jobs |
| `php artisan schedule:run` | Run due scheduled tasks |
| `php artisan key:generate` | Generate the `APP_KEY` |
| `php artisan test` | Run the test suite |
| `php artisan down` / `up` | Enable / disable maintenance mode |

Commands are grouped by namespace: `make:*` (generators), `migrate:*`, `cache:*`, `route:*`, `queue:*`, and so on. Type `php artisan make:` and press Tab in many shells to see completion options, or run `php artisan list make`.

---

## 18.2 Generators

The `make:*` commands create boilerplate files in the correct location with the correct namespace.

| Command | Creates |
|---|---|
| `make:model Post` | A model |
| `make:controller PostController` | A controller |
| `make:migration create_posts_table` | A migration |
| `make:request StorePostRequest` | A form request |
| `make:middleware EnsureUserIsAdmin` | Middleware |
| `make:policy PostPolicy` | A policy |
| `make:seeder PostSeeder` | A seeder |
| `make:factory PostFactory` | A factory |
| `make:job ProcessPodcast` | A queued job |
| `make:mail OrderShipped` | A mailable |
| `make:notification InvoicePaid` | A notification |
| `make:resource PostResource` | An API resource |
| `make:test PostTest` | A test |
| `make:command SendReports` | A console command |

Flags let you generate related files at once:

```bash
php artisan make:model Post -mfsc    # model + migration + factory + seeder + controller
php artisan make:model Post --all    # model plus all related files
php artisan make:controller PostController --resource --model=Post
php artisan make:controller Api/PostController --api   # API resource controller
```

Subfolders are created from slashes: `make:controller Admin/UserController` creates `app/Http/Controllers/Admin/UserController.php`.

You can customize the generated code by publishing the stub templates:

```bash
php artisan stub:publish
```

The stubs appear in a `stubs/` folder at the project root, and Laravel will use your edited versions from then on. Check available options for any generator with `php artisan make:model --help`.

---

## 18.3 Custom commands

Write your own commands for maintenance tasks, imports, reports, or anything you want to run from the terminal or the scheduler.

```bash
php artisan make:command SendWeeklyReport
```

```php
<?php

namespace App\Console\Commands;

use App\Models\User;
use Illuminate\Console\Command;

class SendWeeklyReport extends Command
{
    protected $signature = 'report:send {user : The user ID} {--dry-run : Do not actually send}';

    protected $description = 'Send the weekly report to a user';

    public function handle(): int
    {
        $user = User::find($this->argument('user'));

        if (! $user) {
            $this->error('User not found.');
            return self::FAILURE;
        }

        if ($this->option('dry-run')) {
            $this->info('Dry run only.');
            return self::SUCCESS;
        }

        // ... send the report
        $this->info("Report sent to {$user->email}");

        return self::SUCCESS;
    }
}
```

Run it:

```bash
php artisan report:send 5
php artisan report:send 5 --dry-run
```

Files in `app/Console/Commands` are discovered automatically.

**Signature syntax:**

- `{name}` required argument, `{name?}` optional, `{name=default}` with default
- `{--force}` boolean option, `{--queue=}` option that takes a value

**Input and output helpers:**

```php
$name = $this->ask('What is your name?');
$password = $this->secret('Password?');
if ($this->confirm('Continue?')) { /* ... */ }
$choice = $this->choice('Pick one', ['a', 'b'], 0);

$this->line('Plain text');
$this->info('Green success text');
$this->warn('Yellow warning');
$this->error('Red error');
$this->table(['ID', 'Name'], User::all(['id', 'name'])->toArray());

$this->withProgressBar($users, fn ($user) => $this->process($user));
```

Return `self::SUCCESS` (0) or `self::FAILURE` (1) so scripts and schedulers know whether the command worked.

---

## 18.4 Tinker

**Tinker** is an interactive shell (REPL) where you can run PHP with your whole Laravel application loaded. It is perfect for trying things quickly.

```bash
php artisan tinker
```

Examples inside Tinker:

```php
> App\Models\User::count()
= 12

> $user = App\Models\User::first()
> $user->name
= "Ada Lovelace"

> App\Models\Post::factory()->count(3)->create()

> $user->posts()->create(['title' => 'Test', 'body' => 'Hello'])

> Hash::make('secret')
> now()->addDays(10)
> config('app.name')
> route('posts.index')
```

Tips:

- Use `exit` or press `Ctrl+C` to leave.
- Tinker changes real data in the database configured in `.env`. Be careful, and never experiment on production.
- If you edit code while Tinker is open, restart it to load the changes.
- Run a single expression without opening the shell: `php artisan tinker --execute="echo App\Models\User::count();"`.
- Use it to test queries, factories, services, and mail or notification calls before putting them in controllers.

[Previous](./[17]-Frontend-Stacks.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[19]-Testing-Laravel-Applications.md)