[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[3]-Composer-and-Dependency-Management.md)

*Getting Started*

# Lesson 2 - Running PHP Code

## 2.1 The CLI (Command Line Interface)

PHP can run scripts directly from the terminal without any web server. Save a file as `hello.php`:

```php
<?php
echo "Hello, PHP!";
```

Then run it:

```bash
php hello.php
```

The CLI is the fastest way to experiment with small scripts and is what you'll use throughout most of this course.

---

## 2.2 PHP's Built-in Development Server

For testing web pages without installing Apache or Nginx, PHP includes a lightweight built-in server:

```bash
php -S localhost:8000
```

Run this from the folder containing your `.php` files, then visit `http://localhost:8000` in a browser. This server is meant for local development only, never for production.

---

## 2.3 Running PHP with Apache/Nginx

In production, PHP typically runs as part of a web server setup:
- **Apache** uses the `mod_php` module or PHP-FPM to process `.php` files.
- **Nginx** doesn't run PHP itself — it forwards requests to **PHP-FPM** (FastCGI Process Manager), which executes the code and returns the result.

You won't need to configure this yet, but it's useful to know the pieces involved before we cover deployment later in the course.

---

## 2.4 Choosing the Right Method

- Use the **CLI** for scripts, automation, and testing logic.
- Use the **built-in server** for quick local web page previews.
- Use **Apache/Nginx + PHP-FPM** when deploying a real website.

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./[3]-Composer-and-Dependency-Management.md)
