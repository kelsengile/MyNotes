[Previous](./[3]-Composer-and-Dependency-Management.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./%5B5%5D-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 4 - php.ini And Configuration

## 4.1 What is php.ini?

`php.ini` is PHP's main configuration file. It controls behavior like error reporting, file upload limits, memory limits, and which extensions are enabled. Every PHP installation loads one at startup.

---

## 4.2 Finding Your php.ini File

You can ask PHP directly where its configuration file lives:

```bash
php --ini
```

This prints the loaded configuration file path along with any additional `.ini` files scanned. Editing this file requires restarting the CLI or web server for changes to take effect.

---

## 4.3 Common Settings to Know

| Setting | Purpose |
|---|---|
| `display_errors` | Whether errors are shown in output (on for local dev, off in production) |
| `error_reporting` | Which error levels are reported |
| `memory_limit` | Maximum memory a script may use |
| `upload_max_filesize` | Maximum size of an uploaded file |
| `post_max_size` | Maximum size of POST request data |
| `date.timezone` | Default timezone used by date functions |

---

## 4.4 Changing Settings at Runtime

Some settings can be changed from within a script using `ini_set()`, without touching `php.ini`:

```php
<?php
ini_set('display_errors', '1');
error_reporting(E_ALL);
```

This is handy for local debugging, but production settings should generally live in `php.ini` (or a per-directory override) rather than scattered across code.

---

[Previous](./[3]-Composer-and-Dependency-Management.md) | [Table of Contents](./[0]-Introduction-to-PHP.md) | [Next](./%5B5%5D-Variables-and-Data-Types%20%281%29.md)
