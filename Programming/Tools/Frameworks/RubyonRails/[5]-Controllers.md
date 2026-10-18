[Previous](./[4]-Routing.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[6]-Views-And-Templates.md)

*Basics*

# Lesson 5 - Controllers

## 5.1 Actions

A controller is a Ruby class whose public methods are **actions**. Each action handles one kind of request.

```ruby
# app/controllers/articles_controller.rb
class ArticlesController < ApplicationController
  def index
    @articles = Article.order(created_at: :desc)
  end

  def show
    @article = Article.find(params[:id])
  end

  def new
    @article = Article.new
  end

  def create
    @article = Article.new(article_params)
    if @article.save
      redirect_to @article, notice: "Article created."
    else
      render :new, status: :unprocessable_entity
    end
  end

  private

  def article_params
    params.expect(article: [:title, :body])
  end
end
```

Key ideas:

- **Instance variables** (`@articles`) set in an action are automatically available in the view.
- If an action doesn't call `render` or `redirect_to`, Rails **renders the template** named after the action (`articles/index.html.erb`).
- `private` methods are helpers, not actions, so they are not routable.
- Controllers inherit from `ApplicationController`, which is the place for behavior shared by all controllers.

Generate one with `bin/rails generate controller Articles index show`.

---

## 5.2 Params and strong parameters

`params` contains everything the request sent: path segments, query strings, and form data.

```ruby
# GET /articles/7?format=json&page=2
params[:id]       # "7"      (from the route)
params[:page]     # "2"      (from the query string)
params[:article]  # { "title" => "...", "body" => "..." }  (from a form)
```

Values arrive as **strings**, so convert them when needed (`params[:page].to_i`).

**Strong parameters** protect against *mass assignment* attacks, where a user adds extra fields (like `admin=true`) to a form submission. Rails refuses to pass unpermitted params into `new`, `create`, or `update`. You must whitelist the allowed fields.

Classic style:

```ruby
def article_params
  params.require(:article).permit(:title, :body, :published)
end
```

Newer style (Rails 8), which also returns a 400 error cleanly if the shape is wrong:

```ruby
def article_params
  params.expect(article: [:title, :body, :published])
end
```

Permitting arrays and nested attributes:

```ruby
params.expect(article: [:title, tag_ids: [], comments_attributes: [[:id, :body]]])
```

Never write `params.permit!` (permit everything) on user-facing input.

---

## 5.3 Rendering and redirecting

Every action ends in exactly one response: a **render** or a **redirect**.

**Render** sends a template (or data) as the response to the current request:

```ruby
render :new                                  # templates/articles/new.html.erb
render :show, status: :ok
render json: @article                        # JSON response
render plain: "OK"
render :new, status: :unprocessable_entity   # failed form (HTTP 422)
```

> In newer Rack versions the 422 symbol is also available as `:unprocessable_content`. Use whichever your generated scaffold uses.

**Redirect** tells the browser to make a *new* request to another URL:

```ruby
redirect_to articles_path
redirect_to @article, notice: "Saved."
redirect_to root_path, alert: "Please sign in."
redirect_back fallback_location: root_path
```

Use **redirect after a successful POST/PATCH/DELETE** so refreshing the page doesn't resubmit the form (the Post/Redirect/Get pattern). Use **render with a 422 status** when validation fails, so the form can redisplay with errors and Turbo knows to update the page.

Responding to multiple formats:

```ruby
def show
  @article = Article.find(params[:id])
  respond_to do |format|
    format.html
    format.json { render json: @article }
  end
end
```

Only one `render` or `redirect_to` may run per action. Otherwise you get `AbstractController::DoubleRenderError`.

---

## 5.4 Filters and callbacks

Filters (also called callbacks) run code **before, after, or around** actions. They remove repetition.

```ruby
class ArticlesController < ApplicationController
  before_action :set_article, only: [:show, :edit, :update, :destroy]
  before_action :require_login, except: [:index, :show]

  def show; end   # @article is already set

  private

  def set_article
    @article = Article.find(params[:id])
  end
end
```

| Filter | When it runs |
|---|---|
| `before_action` | Before the action; can halt by rendering or redirecting |
| `after_action` | After the action, before the response is sent |
| `around_action` | Wraps the action (call `yield` for the action itself) |

Options:

- `only:` and `except:` limit which actions are affected.
- `skip_before_action :require_login, only: :index` removes an inherited filter.

An `around_action` example:

```ruby
around_action :log_duration

def log_duration
  started = Time.current
  yield
  Rails.logger.info "Took #{Time.current - started}s"
end
```

**Handling errors for all actions:**

```ruby
class ApplicationController < ActionController::Base
  rescue_from ActiveRecord::RecordNotFound do
    render file: Rails.public_path.join("404.html"), status: :not_found, layout: false
  end
end
```

If a `before_action` calls `render` or `redirect_to`, the chain stops and the action never runs.

> Official reference: [Action Controller Overview](https://guides.rubyonrails.org/action_controller_overview.html)

---

[Previous](./[4]-Routing.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[6]-Views-And-Templates.md)