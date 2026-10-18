[Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[2]-Routing.md)

*Basics*

# Lesson 1 - What Is Laravel

## 1.1 Laravel and the MVC pattern

Laravel is a PHP framework built around the **MVC** (Model-View-Controller) pattern, which splits an application into three responsibilities:

- **Model** — represents your data and business rules. In Laravel, models are usually Eloquent classes that map to database tables.
- **View** — what the user sees. In Laravel, views are Blade templates in `resources/views`.
- **Controller** — receives a request, asks models for data, and returns a view or other response.

A typical request flows like this:

```
Browser → public/index.php → Router (routes/web.php) → Middleware → Controller → Model → View → Response
```

1. The browser sends a request to `public/index.php`, the single entry point of the app.
2. The router matches the URL to a route.
3. Middleware runs (for example, checking that the user is logged in).
4. The controller does the work, using models to read or write the database.
5. A view (or JSON) is returned as the response.

Laravel does not force strict MVC. You can return a view straight from a route, but MVC keeps larger apps organized.

---

## 1.2 PHP and Composer prerequisites

Before creating a project you need:

- **PHP** — Laravel 11 and newer require PHP 8.2 or higher.
- **Composer** — PHP's dependency manager. Laravel itself and all of its packages are installed through it.
- **Node.js and npm** — used to build CSS and JavaScript assets with Vite.
- **A database** — SQLite works out of the box. MySQL, MariaDB, and PostgreSQL are also supported.

Check what you have installed:

```bash
php -v
composer -V
node -v
npm -v
```

Common ways to get a ready-made environment:

- **Laravel Herd** — a native app for macOS and Windows that bundles PHP, Nginx, and Composer.
- **Laravel Sail** — a Docker-based development environment.
- **Manual install** — install PHP and Composer yourself (XAMPP, Laragon, or system packages).

---

## 1.3 Creating a project

There are two common ways to create a project.

**Using Composer:**

```bash
composer create-project laravel/laravel example-app
```

**Using the Laravel installer** (install it once with `composer global require laravel/installer`):

```bash
laravel new example-app
```

The installer asks which starter kit and testing framework you want. Then start the development server:

```bash
cd example-app
php artisan serve
```

Open `http://localhost:8000` to see the welcome page. `artisan` is Laravel's command-line tool, covered in Lesson 18.

During installation Laravel creates a `.env` file and generates an `APP_KEY`, which is used for encryption. If you ever clone a project that lacks one, run:

```bash
cp .env.example .env
php artisan key:generate
```

---

## 1.4 Tour of the project structure

| Folder / File | Purpose |
|---|---|
| `app/` | Your application code: models, controllers, providers |
| `bootstrap/` | Boots the framework. `app.php` configures routing and middleware; `cache/` holds generated cache files |
| `config/` | Configuration files for database, mail, auth, and so on |
| `database/` | Migrations, factories, seeders, and the default SQLite file |
| `public/` | Web server document root: `index.php`, compiled assets, images |
| `resources/` | Uncompiled assets and Blade views (`views/`, `css/`, `js/`) |
| `routes/` | Route definitions: `web.php`, `console.php`, and `api.php` once installed |
| `storage/` | Logs, compiled views, cache, and uploaded files |
| `tests/` | Automated tests (`Feature/` and `Unit/`) |
| `vendor/` | Composer packages. Never edit these and never commit them |
| `.env` | Environment-specific settings and secrets. Never commit this file |
| `artisan` | The command-line entry point |
| `composer.json` | PHP dependencies and autoloading rules |

Starting with Laravel 11, the skeleton is intentionally slim. There is no `app/Http/Kernel.php`; middleware, routing, and exception handling are configured in `bootstrap/app.php`. Optional features such as `routes/api.php` are installed when you need them (for example with `php artisan install:api`).

[Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[2]-Routing.md)