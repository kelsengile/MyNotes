[Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[2]-Ruby-Essentials-For-Rails.md)

*Basics*

# Lesson 1 - What Is Rails

## 1.1 Convention over configuration

Rails makes decisions for you so you don't have to make them yourself. If you name things the way Rails expects, it connects them automatically with no configuration files.

| Piece | Convention | Example |
|---|---|---|
| Model class | Singular, CamelCase | `Article` |
| Database table | Plural, snake_case | `articles` |
| Controller class | Plural, CamelCase + `Controller` | `ArticlesController` |
| Controller file | Plural, snake_case | `app/controllers/articles_controller.rb` |
| View folder | Plural, snake_case | `app/views/articles/` |
| Foreign key | Singular model name + `_id` | `article_id` |

With these names, `Article` knows it is stored in the `articles` table, and `ArticlesController#show` knows it should render `app/views/articles/show.html.erb`.

Rails also follows **DRY** (Don't Repeat Yourself): each piece of knowledge should live in one place. For example, column names are read from the database, so you never list them again in the model.

You can always override a convention when you need to, for example `self.table_name = "legacy_articles"`. The idea is that you only write configuration for the exceptions.

---

## 1.2 MVC in Rails

Rails organizes code into **Model–View–Controller**:

- **Model**: data and business rules. In Rails, models are usually Active Record classes backed by database tables.
- **View**: what the user sees, usually HTML templates (`.html.erb`).
- **Controller**: receives a request, asks models for data, and chooses a view (or redirect) as the response.

A request travels like this:

```text
Browser
   │  GET /articles/1
   ▼
Router (config/routes.rb)          → picks ArticlesController#show
   ▼
Controller (articles_controller.rb) → @article = Article.find(1)
   ▼
Model (article.rb) ⇄ Database       → loads the row
   ▼
View (articles/show.html.erb)       → renders HTML using @article
   ▼
Browser receives the HTML response
```

The router is not part of MVC itself, but it is the first stop: it maps a URL and HTTP verb to a controller action.

---

## 1.3 Installing Ruby and Rails

**Requirements**

- Ruby 3.2 or newer (required by Rails 8)
- Git
- A database: new apps use **SQLite** by default, so no extra install is needed to start
- A JavaScript runtime is *not* required by default, because Rails uses import maps instead of a bundler

**Install Ruby using a version manager** so you can switch Ruby versions per project. Popular choices are `mise`, `rbenv`, and `asdf`. On macOS you can use Homebrew to install them, and on Linux your package manager. On Windows, use **WSL2** (Windows Subsystem for Linux) and follow the Linux steps.

Check Ruby:

```bash
ruby -v
```

Install Rails as a gem:

```bash
gem install rails
```

Verify:

```bash
rails -v
```

If you see a version number such as `Rails 8.x.x`, you are ready.

---

## 1.4 Generating a new app and touring it

Create an app called `blog`:

```bash
rails new blog
cd blog
```

Useful options:

```bash
rails new blog -d postgresql      # use PostgreSQL instead of SQLite
rails new blog --css tailwind     # add Tailwind CSS
rails new api_app --api           # lightweight API-only app
```

Start the development server:

```bash
bin/rails server
```

Open `http://localhost:3000` and you will see the Rails welcome page.

**The folders you will use most**

| Path | Purpose |
|---|---|
| `app/models` | Models (Active Record classes) |
| `app/views` | Templates and layouts |
| `app/controllers` | Controllers |
| `app/helpers` | View helper methods |
| `app/jobs`, `app/mailers` | Background jobs and mailers |
| `app/javascript` | JavaScript (Stimulus controllers, etc.) |
| `config/routes.rb` | The route table |
| `config/` | Environment, database, and credentials settings |
| `db/` | Migrations, `schema.rb`, `seeds.rb` |
| `test/` | Automated tests |
| `Gemfile` | The gems your app depends on |

**Your first page.** Generate a controller with one action:

```bash
bin/rails generate controller Welcome index
```

Open `config/routes.rb` and make it the home page:

```ruby
Rails.application.routes.draw do
  root "welcome#index"
end
```

Edit `app/views/welcome/index.html.erb`:

```erb
<h1>Hello, Rails!</h1>
<p>The time is <%= Time.current.strftime("%H:%M") %>.</p>
```

Refresh the browser. You just touched the router, a controller, and a view, which is the full MVC path (the model comes in Lesson 8).

> Official reference: [Getting Started with Rails](https://guides.rubyonrails.org/getting_started.html)

---

[Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[2]-Ruby-Essentials-For-Rails.md)