[Previous](./[19]-Testing-Laravel-Applications.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[21]-Next-Steps-With-Laravel.md)

*Quality And Delivery*

# Lesson 20 - Deployment

## 20.1 Environment configuration

Deploying means moving your app from your computer to a server that users can reach. Start by preparing the environment settings.

Production `.env` essentials:

```env
APP_NAME="My App"
APP_ENV=production
APP_DEBUG=false
APP_KEY=base64:...
APP_URL=https://example.com

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=my_app
DB_USERNAME=app_user
DB_PASSWORD=strong-password

QUEUE_CONNECTION=redis
CACHE_STORE=redis
SESSION_DRIVER=redis
LOG_LEVEL=warning
```

Rules:

- **`APP_DEBUG=false` in production.** Debug mode shows stack traces, environment details, and secrets to anyone who triggers an error.
- **Never commit `.env`.** It is in `.gitignore`. Create it on the server separately.
- **Generate an `APP_KEY` once** (`php artisan key:generate`) and keep it. Changing it later invalidates encrypted data and sessions.
- Use a **dedicated database user** with only the permissions your app needs.
- Make `storage/` and `bootstrap/cache/` writable by the web server user.
- Server requirements: a supported PHP version with the extensions Laravel needs (such as `mbstring`, `openssl`, `pdo`, `tokenizer`, `xml`, `ctype`, `json`).
- Point the web server's **document root at the `public` folder**, never at the project root.

---

## 20.2 Optimization commands

Laravel can cache work that normally happens on every request.

Typical production deploy sequence:

```bash
git pull origin main
composer install --no-dev --optimize-autoloader
npm ci && npm run build        # or build assets elsewhere and upload them
php artisan migrate --force
php artisan optimize           # caches config, routes, events, and views
php artisan queue:restart
```

Individual commands:

| Command | Caches |
|---|---|
| `php artisan config:cache` | Merges all config files into one |
| `php artisan route:cache` | Compiles routes (faster routing) |
| `php artisan view:cache` | Precompiles Blade views |
| `php artisan event:cache` | Event and listener mappings |
| `php artisan optimize` | Runs the main caches together |
| `php artisan optimize:clear` | Removes all caches |

Important cautions:

- After `config:cache`, the `env()` function returns `null` outside of config files. Only call `env()` inside files in `config/`, and use `config('app.name')` everywhere else.
- Route caching does not work with closure-based routes. Use controllers for routes you want cached.
- Run `php artisan optimize:clear` if something behaves strangely after changing configuration.
- `migrate --force` is required in production because Laravel refuses to run migrations non-interactively there without it. Take a database backup before migrating.
- Consider maintenance mode during risky deployments: `php artisan down` and `php artisan up`.

---

## 20.3 Hosting and Forge

Common ways to host a Laravel app:

| Option | Description |
|---|---|
| **VPS** (DigitalOcean, Linode, Hetzner, AWS EC2) | You get a server and manage it: Nginx or Apache, PHP-FPM, database, SSL |
| **Laravel Forge** | A paid service that provisions and manages your own VPS: installs PHP, Nginx, MySQL, Redis, SSL certificates, queue workers, scheduler, and push-to-deploy |
| **Laravel Cloud / Vapor** | Official managed and serverless platforms from the Laravel team |
| **Platform services** | Hosts such as Railway, Render, or Fly.io that run your app from Git or a container |
| **Shared hosting** | Cheapest, but limited. You must point the domain at `public/`, or move that folder's contents and edit the paths in `index.php` |

**Basic Nginx site for Laravel:**

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/my-app/public;

    index index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

Add HTTPS with a free certificate from Let's Encrypt (for example `certbot --nginx`). Automate deployments with a script, GitHub Actions, or Forge's deploy hooks so every release follows the same steps. Always keep backups and monitor logs (`storage/logs/laravel.log`) and, ideally, use an error tracker such as Sentry or Flare.

---

## 20.4 Queue workers in production

In production, queue workers and the scheduler must run continuously. Use **Supervisor**, a process monitor that restarts workers if they crash or the server reboots.

Install it on Ubuntu:

```bash
sudo apt-get install supervisor
```

Create `/etc/supervisor/conf.d/laravel-worker.conf`:

```ini
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/my-app/artisan queue:work redis --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=www-data
numprocs=2
redirect_stderr=true
stdout_logfile=/var/www/my-app/storage/logs/worker.log
stopwaitsecs=3600
```

Load it:

```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start "laravel-worker:*"
```

- `numprocs=2` runs two worker processes.
- `--max-time=3600` makes each worker exit after an hour so Supervisor restarts it fresh.
- Run `php artisan queue:restart` during every deployment so workers pick up the new code. Supervisor restarts them automatically.

**Scheduler:** add the cron entry from Lesson 14 for the same user:

```
* * * * * cd /var/www/my-app && php artisan schedule:run >> /dev/null 2>&1
```

**Monitor failures:** check `php artisan queue:failed`, and consider **Laravel Horizon** (Redis only) for a dashboard, metrics, and automatic worker balancing. Forge and Cloud can configure workers and the scheduler for you with a few clicks.

[Previous](./[19]-Testing-Laravel-Applications.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[21]-Next-Steps-With-Laravel.md)