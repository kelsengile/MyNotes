[Previous](./[18]-Testing-Rails-Applications.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[20]-Deployment.md)

*Quality And Delivery*

# Lesson 19 - Security And Performance

## 19.1 Built-in protections

Rails defends against many common attacks by default. Knowing what it does (and what you must still do) keeps your app safe.

| Threat | Rails protection | Your responsibility |
|---|---|---|
| **CSRF** (forged requests) | Authenticity tokens in forms (`csrf_meta_tags`) | Don't disable it; use `button_to`/`form_with` for state changes |
| **XSS** (injected scripts) | `<%= %>` escapes HTML | Avoid `raw` / `html_safe` on user input |
| **SQL injection** | Query methods use bound parameters | Never interpolate user input into SQL strings |
| **Mass assignment** | Strong parameters | Permit only fields you intend |
| **Session theft** | Encrypted, signed cookies | Use HTTPS; `reset_session` on login |
| **Insecure transport** | `force_ssl` / `assume_ssl` settings | Enable them in production |

Production settings:

```ruby
# config/environments/production.rb
config.force_ssl = true      # redirect HTTP → HTTPS, secure cookies, HSTS
```

Other protections and habits:

- **Filter sensitive params from logs** in `config/initializers/filter_parameter_logging.rb` (`:password`, `:token`, `:secret`).
- **Content Security Policy** in `config/initializers/content_security_policy.rb` restricts where scripts and styles can load from.
- **Secrets** belong in encrypted credentials (Lesson 20), never in Git.
- **Scan your code and dependencies:**

```bash
bin/brakeman                # static analysis for Rails vulnerabilities
bundle audit                # known vulnerable gems (bundler-audit gem)
```

- **Keep Rails and gems updated** to receive security fixes.
- Validate uploads, rate-limit login attempts (Rails 8 offers `rate_limit` in controllers), and always authorize access (Lesson 13).

```ruby
class SessionsController < ApplicationController
  rate_limit to: 10, within: 3.minutes, only: :create
end
```

---

## 19.2 Caching

Caching stores the result of slow work so it can be reused.

**Fragment caching** caches part of a view:

```erb
<% cache @article do %>
  <h2><%= @article.title %></h2>
  <%= render @article.comments %>
<% end %>
```

The cache key includes `updated_at`, so changing the article creates a new entry automatically. For **Russian doll caching**, nested fragments reuse unchanged inner parts. Make child changes expire the parent with `touch`:

```ruby
class Comment < ApplicationRecord
  belongs_to :article, touch: true
end
```

**Low-level caching** stores any Ruby value:

```ruby
stats = Rails.cache.fetch("dashboard_stats", expires_in: 10.minutes) do
  ExpensiveReport.generate
end
```

**HTTP caching** lets browsers skip work using ETags:

```ruby
def show
  @article = Article.find(params[:id])
  fresh_when @article          # responds 304 Not Modified if unchanged
end
```

**Cache stores** (set in `config/environments/*.rb`):

| Store | Notes |
|---|---|
| `:memory_store` | In-process; good for development |
| `:file_store` | Files on disk |
| `:solid_cache_store` | Database-backed; the default in Rails 8 production |
| `:redis_cache_store` | Redis-backed |

In development, caching is off until you toggle it:

```bash
bin/rails dev:cache
```

Remember: "There are only two hard things in computer science: cache invalidation and naming things." Prefer keys that change when data changes, rather than manual expiration.

---

## 19.3 Database indexing

An **index** lets the database find rows without scanning the whole table, like a book's index. Missing indexes are one of the most common causes of slow apps.

Add indexes in migrations:

```ruby
add_index :articles, :slug, unique: true
add_index :comments, :article_id                  # foreign keys!
add_index :articles, [:user_id, :created_at]      # composite index
```

`t.references` and `add_reference` create an index for you by default.

Index columns that you:

- Use in `WHERE` conditions (`where(slug: ...)`)
- Use in `ORDER BY` on large tables
- Join on (**foreign keys**)
- Need unique (`unique: true`, which also enforces data integrity)

In a **composite** index, the column order matters. The index on `[user_id, created_at]` helps queries by `user_id`, or by `user_id` and `created_at`, but not by `created_at` alone.

Trade-offs: indexes speed up reads but slow down writes and use disk space. Don't index every column, only those your queries use.

Check how a query is executed:

```ruby
Article.where(slug: "hello").explain
```

Look for "Seq Scan" (PostgreSQL) or "SCAN" (SQLite) on big tables, which signals a missing index. An "Index Scan" or "SEARCH ... USING INDEX" means the index is working.

---

## 19.4 Profiling

Measure before you optimize. Guessing wastes time.

**Start with the log** (`log/development.log`). Each request shows the total time, view time, Active Record time, and every SQL query. Look for slow or repeated queries (the N+1 problem from Lesson 10).

**Tools:**

| Tool | Use |
|---|---|
| `rack-mini-profiler` gem | Shows a timing badge on each page, with SQL breakdown |
| `bullet` gem | Warns about N+1 queries and unused eager loading |
| `stackprof` / `ruby-prof` | Find slow Ruby methods |
| `derailed_benchmarks` | Memory and boot analysis |
| APM services (Skylight, Scout, New Relic, Datadog) | Production performance monitoring |

**Quick timing in code:**

```ruby
require "benchmark"

time = Benchmark.realtime do
  Article.includes(:comments).to_a
end
puts "Took #{(time * 1000).round(1)} ms"
```

```ruby
ActiveSupport::Notifications.subscribe("sql.active_record") do |*, payload|
  Rails.logger.info "SQL: #{payload[:sql]}"
end
```

**Common wins, in rough order:**

1. Fix N+1 queries with `includes`.
2. Add missing indexes.
3. Select only needed columns (`pluck`, `select`).
4. Paginate big lists (`pagy` gem).
5. Cache expensive fragments and computations.
6. Move slow work to background jobs (Lesson 15).
7. Use `find_each` when processing many records, so they're loaded in batches:

```ruby
Article.find_each(batch_size: 500) { |article| article.reindex }
```

Always profile in **production-like** conditions (production mode, realistic data volume), since development mode reloads code and is slower.

> Official references: [Securing Rails Applications](https://guides.rubyonrails.org/security.html), [Caching with Rails](https://guides.rubyonrails.org/caching_with_rails.html), [Brakeman](https://brakemanscanner.org/)

---

[Previous](./[18]-Testing-Rails-Applications.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[20]-Deployment.md)