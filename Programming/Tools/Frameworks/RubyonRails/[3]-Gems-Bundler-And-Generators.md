[Previous](./[2]-Ruby-Essentials-For-Rails.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[4]-Routing.md)

*Basics*

# Lesson 3 - Gems, Bundler, And Generators

## 3.1 The gem ecosystem

A **gem** is a packaged Ruby library. Rails itself is a collection of gems (`activerecord`, `actionpack`, `activejob`, and more). Gems are published on [rubygems.org](https://rubygems.org).

Basic commands:

```bash
gem install colorize     # install a gem globally
gem list                 # list installed gems
gem search rails         # search rubygems.org
gem info rails           # details about a gem
```

In a Rails app you rarely use `gem install` directly, because Bundler manages gems per project (see 3.2).

**Choosing a gem wisely**

- Check recent releases and commit activity.
- Check download counts and open issues.
- Read the README and the license.
- Prefer a small, well-maintained gem over a large abandoned one.
- Every gem is code you depend on, so add only what you need.

---

## 3.2 Gemfile and Bundler

**Bundler** reads your `Gemfile`, resolves compatible versions, and installs them. Every Rails app has a `Gemfile`:

```ruby
source "https://rubygems.org"

gem "rails", "~> 8.0"
gem "pg"                                  # PostgreSQL adapter

group :development, :test do
  gem "debug"
end

group :development do
  gem "web-console"
end
```

**Version constraints**

| Constraint | Meaning |
|---|---|
| `gem "pg"` | Any version (not recommended for important gems) |
| `gem "rails", "8.0.1"` | Exactly this version |
| `gem "rails", "~> 8.0"` | `>= 8.0` and `< 9.0` |
| `gem "rails", "~> 8.0.1"` | `>= 8.0.1` and `< 8.1` |

**Key commands**

```bash
bundle install          # install gems and write Gemfile.lock
bundle add devise       # add a gem to the Gemfile and install it
bundle update devise    # upgrade one gem
bundle exec rspec       # run a command using the project's gem versions
bundle outdated         # show gems with newer versions
```

`Gemfile.lock` records the exact versions that were installed. **Commit it to Git** so everyone, including your production server, uses identical versions.

`group` blocks control where a gem is loaded, so test tools don't ship to production.

---

## 3.3 Rails generators

Generators create files for you following Rails conventions. Run them with `bin/rails generate` (or `bin/rails g`).

```bash
bin/rails generate model Article title:string body:text
bin/rails generate controller Articles index show
bin/rails generate migration AddPublishedToArticles published:boolean
bin/rails generate scaffold Comment body:text article:references
bin/rails generate --help        # list all generators
```

What each one creates:

| Generator | Creates |
|---|---|
| `model` | Model class, migration, and test files |
| `controller` | Controller, view folder (and views for listed actions), helper, route entries |
| `migration` | A timestamped migration file |
| `scaffold` | A complete resource: model, migration, controller, views, routes, tests |
| `job`, `mailer`, `channel` | Their respective class and test files |

Attribute syntax is `name:type`, such as `price:decimal`, `born_on:date`, and `user:references` (adds a foreign key).

To undo a generator, use `destroy`:

```bash
bin/rails destroy model Article
```

Scaffolds are excellent for learning and quick prototypes, but read the code they produce. It shows idiomatic Rails.

---

## 3.4 The Rails console

The console is an interactive Ruby session with your whole app loaded:

```bash
bin/rails console      # or: bin/rails c
```

```ruby
Article.count
Article.create(title: "First", body: "Hello")
Article.where(title: "First").first
```

**Helpful options and tricks**

- `bin/rails console --sandbox` rolls back all database changes when you exit, which is safe for experiments.
- `reload!` reloads your code after edits.
- `app.articles_path` gives route helpers (`app` is an integration session).
- `helper.number_to_currency(10)` gives access to view helpers.
- `_` holds the last result.
- `Article.new.methods - Object.instance_methods` lists a model's own methods.
- `exit` or `Ctrl+D` leaves the console.

Be careful in a *production* console. It runs against real data, and changes are real.

> Official references: [Bundler](https://bundler.io/), [Command Line guide](https://guides.rubyonrails.org/command_line.html)

---

[Previous](./[2]-Ruby-Essentials-For-Rails.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[4]-Routing.md)