[Previous](./[3]-Gems-Bundler-And-Generators.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[5]-Controllers.md)

*Basics*

# Lesson 4 - Routing

## 4.1 Resourceful routes

The router lives in `config/routes.rb` and maps an HTTP verb plus a path to a controller action.

The most common way to define routes is `resources`:

```ruby
Rails.application.routes.draw do
  resources :articles
  root "articles#index"
end
```

That one line creates seven routes:

| Verb | Path | Controller#Action | Purpose |
|---|---|---|---|
| GET | `/articles` | `articles#index` | List all |
| GET | `/articles/new` | `articles#new` | Form for a new record |
| POST | `/articles` | `articles#create` | Create a record |
| GET | `/articles/:id` | `articles#show` | Show one |
| GET | `/articles/:id/edit` | `articles#edit` | Form to edit |
| PATCH/PUT | `/articles/:id` | `articles#update` | Update a record |
| DELETE | `/articles/:id` | `articles#destroy` | Delete a record |

This pattern is **REST**: URLs name *things* (nouns), and HTTP verbs say what to do.

Limit the routes you generate:

```ruby
resources :articles, only: [:index, :show]
resources :comments, except: [:edit, :update]
```

A **singular resource** has no id, such as a user's profile:

```ruby
resource :profile        # /profile, controller is ProfilesController
```

Simple one-off routes:

```ruby
get "about", to: "pages#about"
```

---

## 4.2 Named routes and helpers

Each route gets a **helper method** for building paths in code, so you never hard-code URLs.

View all routes:

```bash
bin/rails routes
bin/rails routes -g article      # filter by text
```

For `resources :articles` you get:

| Helper | Returns |
|---|---|
| `articles_path` | `/articles` |
| `new_article_path` | `/articles/new` |
| `article_path(@article)` | `/articles/1` |
| `edit_article_path(@article)` | `/articles/1/edit` |

Each has a `_url` twin (`articles_url`) that returns the full address including host. Use `_path` in views and `_url` in redirects, emails, and APIs.

```erb
<%= link_to "All articles", articles_path %>
<%= link_to "Read more", article_path(@article) %>
<%= link_to "Read more", @article %>   <%# shortcut, same result %>
```

Name a custom route with `as:`:

```ruby
get "about", to: "pages#about", as: :about   # about_path
get "/login", to: "sessions#new", as: :login
```

Other helpful options: `root "articles#index"` creates `root_path`, and `redirect("/new-path")` handles old URLs.

---

## 4.3 Nested resources

When one resource only makes sense inside another, nest it:

```ruby
resources :articles do
  resources :comments
end
```

This gives routes like `/articles/5/comments` and `/articles/5/comments/9`, with helpers `article_comments_path(@article)` and `article_comment_path(@article, @comment)`.

In the controller, the parent's id arrives as `params[:article_id]`:

```ruby
class CommentsController < ApplicationController
  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.create(comment_params)
    redirect_to @article
  end
end
```

**Rule of thumb:** nest no deeper than one level. Deep URLs are awkward. Use **shallow nesting** so only collection routes (`index`, `new`, `create`) are nested and member routes stay short:

```ruby
resources :articles do
  resources :comments, shallow: true
end
# /articles/5/comments    (index, create)
# /comments/9             (show, update, destroy)
```

---

## 4.4 Constraints and namespaces

**Namespaces** group routes under a URL prefix, controller module, and helper prefix:

```ruby
namespace :admin do
  resources :articles
end
# /admin/articles → Admin::ArticlesController
# helper: admin_articles_path
```

The controller lives at `app/controllers/admin/articles_controller.rb`:

```ruby
class Admin::ArticlesController < ApplicationController
end
```

**`scope`** changes only part of that: the path, the module, or both:

```ruby
scope "/blog" do
  resources :articles          # /blog/articles → ArticlesController
end

scope module: "api" do
  resources :articles          # /articles → Api::ArticlesController
end
```

**Extra actions** on a resource:

```ruby
resources :articles do
  member do
    patch :publish             # PATCH /articles/1/publish → articles#publish
  end
  collection do
    get :search                # GET /articles/search
  end
end
```

**Constraints** restrict when a route matches:

```ruby
# Only numeric ids
get "articles/:id", to: "articles#show", constraints: { id: /\d+/ }

# Only for a specific subdomain
constraints subdomain: "api" do
  resources :articles
end

# Custom rule using a lambda
constraints ->(request) { request.session[:admin] } do
  resources :reports
end
```

Remember that route constraints are not a replacement for authorization checks in controllers (see Lesson 13).

> Official reference: [Rails Routing from the Outside In](https://guides.rubyonrails.org/routing.html)

---

[Previous](./[3]-Gems-Bundler-And-Generators.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[5]-Controllers.md)