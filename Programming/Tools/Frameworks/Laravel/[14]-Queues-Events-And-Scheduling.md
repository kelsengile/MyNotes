[Previous](./[13]-Service-Container-And-Providers.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[15]-Mail-Notifications-And-File-Storage.md)

*Application Features*

# Lesson 14 - Queues, Events, And Scheduling

## 14.1 Jobs and queues

Some tasks are slow: sending emails, resizing images, calling external APIs. Doing them during the web request makes users wait. A **queue** lets you push the work into the background to be handled by a separate **worker** process.

Create a job:

```bash
php artisan make:job ProcessPodcast
```

```php
<?php

namespace App\Jobs;

use App\Models\Podcast;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Queue\Queueable;

class ProcessPodcast implements ShouldQueue
{
    use Queueable;

    public int $tries = 3;      // retry up to 3 times
    public int $timeout = 120;  // seconds

    public function __construct(public Podcast $podcast) {}

    public function handle(): void
    {
        // do the slow work here
    }
}
```

Dispatch it:

```php
ProcessPodcast::dispatch($podcast);
ProcessPodcast::dispatch($podcast)->delay(now()->addMinutes(10));
ProcessPodcast::dispatch($podcast)->onQueue('audio');
```

The queue backend is chosen with `QUEUE_CONNECTION` in `.env`:

| Driver | Notes |
|---|---|
| `sync` | Runs immediately, no background (useful for local testing) |
| `database` | Stores jobs in a table, the default in new apps |
| `redis` | Fast, popular for production |
| `sqs`, `beanstalkd` | Other supported services |

For the `database` driver, run `php artisan migrate` so the `jobs` and `failed_jobs` tables exist.

Handling failures:

```php
public function failed(\Throwable $exception): void
{
    // notify someone
}
```

```bash
php artisan queue:failed         # list failed jobs
php artisan queue:retry all      # retry all of them
```

Because jobs are serialized, pass Eloquent models, not large objects. Laravel stores the model ID and reloads it when the job runs.

---

## 14.2 Events and listeners

**Events** announce that something happened (`OrderShipped`). **Listeners** react to it (send an email, update stock). The two sides do not know about each other, which keeps code loosely coupled.

```bash
php artisan make:event OrderShipped
php artisan make:listener SendShipmentNotification --event=OrderShipped
```

```php
// app/Events/OrderShipped.php
class OrderShipped
{
    use Dispatchable, SerializesModels;

    public function __construct(public Order $order) {}
}
```

```php
// app/Listeners/SendShipmentNotification.php
class SendShipmentNotification
{
    public function handle(OrderShipped $event): void
    {
        // $event->order
    }
}
```

Dispatch the event:

```php
use App\Events\OrderShipped;

OrderShipped::dispatch($order);
// or: event(new OrderShipped($order));
```

**Auto-discovery.** In Laravel 11 and newer, listeners in `app/Listeners` are discovered automatically: any `handle` method whose first parameter is an event type is registered for that event. In older versions you registered pairs in `EventServiceProvider`.

To run a listener in the background, implement `ShouldQueue`:

```php
class SendShipmentNotification implements ShouldQueue { ... }
```

Eloquent models also fire events automatically (`creating`, `created`, `updating`, `deleted`, and so on), which you can hook into with observers (`php artisan make:observer`).

---

## 14.3 Task scheduling

Instead of one cron entry per task, you define all scheduled tasks in code and add a single cron entry.

Define the schedule in `routes/console.php`:

```php
use Illuminate\Support\Facades\Schedule;

Schedule::command('emails:send')->daily();
Schedule::job(new PruneOldRecords)->weeklyOn(1, '03:00');
Schedule::call(fn () => DB::table('sessions')->where('last_activity', '<', now()->subDay()->timestamp)->delete())
    ->hourly();

Schedule::command('reports:generate')
    ->dailyAt('06:30')
    ->timezone('Asia/Manila')
    ->withoutOverlapping()
    ->onOneServer();
```

Common frequency methods: `everyMinute()`, `everyFiveMinutes()`, `hourly()`, `daily()`, `dailyAt('13:00')`, `weekly()`, `monthly()`, `cron('* * * * *')`.

Useful constraints: `->weekdays()`, `->between('8:00', '17:00')`, `->when(fn () => ...)`, `->runInBackground()`.

Add this single cron entry on the server (it runs every minute and Laravel decides what is due):

```
* * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1
```

Helpful commands:

```bash
php artisan schedule:list   # show scheduled tasks and next run times
php artisan schedule:work   # run the scheduler locally in the foreground
php artisan schedule:test   # pick and run a task manually
```

---

## 14.4 Queue workers

A queued job only runs when a **worker** is running.

```bash
php artisan queue:work
php artisan queue:work --queue=high,default   # process "high" first
php artisan queue:work --tries=3 --timeout=90
php artisan queue:work --stop-when-empty
```

Important facts:

- The worker is a long-running process that keeps your application in memory. After you deploy new code, you must restart workers so they load it:

```bash
php artisan queue:restart
```

- `queue:listen` reloads code on every job (no restart needed, but slower). Use it only in development. For local work you can run `composer run dev` in a fresh project, which starts the server, queue worker, and Vite together.
- If a worker crashes, nothing restarts it. In production, use a process monitor like **Supervisor** to keep it alive (Lesson 20).
- Use `--memory` and `--max-time` options to make workers recycle themselves regularly.
- Jobs that fail more than their `tries` limit go to the `failed_jobs` table.

Process several queues with several workers when you need more throughput. Redis with **Laravel Horizon** gives a dashboard and automatic balancing.

[Previous](./[13]-Service-Container-And-Providers.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[15]-Mail-Notifications-And-File-Storage.md)