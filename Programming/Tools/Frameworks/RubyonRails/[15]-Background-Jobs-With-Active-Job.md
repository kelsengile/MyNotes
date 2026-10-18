[Previous](./[14]-Active-Storage-And-Action-Mailer.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[16]-Hotwire-Turbo-And-Stimulus.md)

*Application Features*

# Lesson 15 - Background Jobs With Active Job

Some work is too slow to do while the user waits: sending email, resizing images, calling external APIs, generating reports. **Active Job** lets you move that work into the background with one consistent API, no matter which queue system runs it.

## 15.1 Defining jobs

Generate a job:

```bash
bin/rails generate job ProcessImage
```

```ruby
# app/jobs/process_image_job.rb
class ProcessImageJob < ApplicationJob
  queue_as :default

  def perform(article)
    article.cover.variant(resize_to_limit: [800, 800]).processed
  end
end
```

Run it:

```ruby
ProcessImageJob.perform_later(article)   # enqueue, returns immediately
ProcessImageJob.perform_now(article)     # run right now, in this process
```

Key points:

- `perform` receives the arguments you pass to `perform_later`.
- Active Record objects are passed using **GlobalID**: Rails stores a reference like `gid://app/Article/1` and reloads the record when the job runs. Pass records or simple values (strings, numbers, hashes, arrays), not complex objects.
- If the record is deleted before the job runs, you get `ActiveJob::DeserializationError`. You can handle it with `discard_on`.
- `queue_as :low_priority` sends work to a named queue so you can prioritize them.
- Keep jobs small, and make them **idempotent** (safe to run twice), because retries and crashes can cause repeats.

Common triggers:

```ruby
UserMailer.welcome(user).deliver_later           # mailers use jobs too
after_commit { ProcessImageJob.perform_later(self) }
```

---

## 15.2 Queue adapters

The **queue adapter** is the system that stores and runs the jobs. Active Job supports several:

| Adapter | Use |
|---|---|
| `:async` | In-process thread pool; jobs are lost on restart. Simple development default. |
| `:inline` | Runs immediately, no queue. Handy in tests/scripts. |
| `:test` | Records jobs without running them (used in tests). |
| `:solid_queue` | Database-backed; the Rails 8 default for new apps. |
| `:sidekiq` | Redis-backed; very fast and widely used. |
| `:good_job`, `:resque` | Other popular options. |

Set it in config:

```ruby
# config/environments/production.rb
config.active_job.queue_adapter = :solid_queue
```

**Solid Queue** (new in Rails 8's default stack) uses your database, so there's no Redis to run. Start workers with:

```bash
bin/jobs
```

New Rails 8 apps can also run it inside the web server process (set `SOLID_QUEUE_IN_PUMA=true`), which is convenient for small deployments. Use separate worker processes as you scale.

**Sidekiq** setup in brief:

```ruby
# Gemfile
gem "sidekiq"

# config/application.rb
config.active_job.queue_adapter = :sidekiq
```

```bash
bundle exec sidekiq
```

Without a running worker (or Puma plugin), jobs will sit in the queue and never run.

---

## 15.3 Retries

Networks fail. Active Job can retry or give up for you:

```ruby
class SyncCrmJob < ApplicationJob
  queue_as :default

  retry_on Net::OpenTimeout, wait: :polynomially_longer, attempts: 5
  retry_on ActiveRecord::Deadlocked, wait: 5.seconds, attempts: 3
  discard_on ActiveJob::DeserializationError

  def perform(user)
    CrmClient.push(user)
  end
end
```

| Method | Purpose |
|---|---|
| `retry_on Error, wait:, attempts:` | Try again later after the given error |
| `discard_on Error` | Silently drop the job for errors that can't be fixed by retrying |
| `rescue_from Error` | Custom handling (log, notify, re-raise) |

`wait:` accepts a duration (`30.seconds`) or `:polynomially_longer`, which increases the delay each attempt.

Run custom logic when retries are exhausted:

```ruby
retry_on SomeError, attempts: 3 do |job, error|
  ErrorReporter.notify(error, job: job.class.name)
end
```

Design tips:

- Make jobs idempotent so a retry doesn't double-charge or double-email.
- Don't swallow exceptions, so failures stay visible.
- Monitor failures (Solid Queue and Sidekiq both provide dashboards or tooling).

---

## 15.4 Scheduling

**Delay a single job:**

```ruby
ReminderJob.set(wait: 1.hour).perform_later(user)
ReminderJob.set(wait_until: Date.tomorrow.noon).perform_later(user)
ReminderJob.set(queue: :urgent).perform_later(user)
```

**Recurring jobs** (e.g. nightly cleanup). With Solid Queue, define them in `config/recurring.yml`:

```yaml
production:
  clear_old_sessions:
    class: ClearOldSessionsJob
    queue: background
    schedule: every day at 3am

  hourly_report:
    command: "Report.generate_hourly"
    schedule: every hour
```

Other approaches:

- **Sidekiq**: the `sidekiq-cron` or `sidekiq-scheduler` gems.
- **System cron**: the `whenever` gem generates crontab entries.
- A hosting platform's own scheduler running `bin/rails runner "SomeJob.perform_later"`.

Tip: a job triggered by a scheduler should be safe if it runs twice or late. Never assume it ran exactly on time.

> Official reference: [Active Job Basics](https://guides.rubyonrails.org/active_job_basics.html)

---

[Previous](./[14]-Active-Storage-And-Action-Mailer.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[16]-Hotwire-Turbo-And-Stimulus.md)