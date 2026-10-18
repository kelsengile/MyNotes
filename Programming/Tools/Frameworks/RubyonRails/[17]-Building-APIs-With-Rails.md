[Previous](./[16]-Hotwire-Turbo-And-Stimulus.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[18]-Testing-Rails-Applications.md)

*Application Features*

# Lesson 17 - Building APIs With Rails

An **API** returns data (usually JSON) for other programs: mobile apps, JavaScript front ends, or other services.

## 17.1 API mode

Create an API-only app:

```bash
rails new my_api --api
```

API mode gives you a **slimmer** stack:

- Controllers inherit from `ActionController::API` (not `Base`), with no view or cookie/session helpers.
- Fewer middleware, and no asset pipeline or view layouts.
- Generators produce JSON-friendly code.

You can also add API controllers to a normal app (see 17.4).

```ruby
class ArticlesController < ApplicationController
  before_action :set_article, only: [:show, :update, :destroy]

  def index
    render json: Article.order(created_at: :desc)
  end

  def show
    render json: @article
  end

  def create
    article = Article.new(article_params)
    if article.save
      render json: article, status: :created, location: article
    else
      render json: { errors: article.errors.full_messages }, status: :unprocessable_entity
    end
  end

  def update
    if @article.update(article_params)
      render json: @article
    else
      render json: { errors: @article.errors.full_messages }, status: :unprocessable_entity
    end
  end

  def destroy
    @article.destroy
    head :no_content
  end

  private

  def set_article
    @article = Article.find(params[:id])
  end

  def article_params
    params.expect(article: [:title, :body])
  end
end
```

Use proper **status codes**: 200 OK, 201 Created, 204 No Content, 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 422 Unprocessable Entity.

**CORS.** If a browser app on a different origin calls your API, enable the `rack-cors` gem:

```ruby
# config/initializers/cors.rb
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins "https://myfrontend.example.com"
    resource "*", headers: :any, methods: [:get, :post, :patch, :put, :delete, :options]
  end
end
```

Test it with curl:

```bash
curl -X POST http://localhost:3000/articles \
  -H "Content-Type: application/json" \
  -d '{"article":{"title":"Hi","body":"There"}}'
```

---

## 17.2 Serializers

`render json: record` calls `as_json`, which exposes **every** column. Control what is returned.

**Quick option:** `as_json`:

```ruby
render json: @article.as_json(only: [:id, :title], methods: [:summary])
render json: @articles.as_json(only: [:id, :title], include: { comments: { only: :body } })
```

**Jbuilder**: templates for JSON (included in many Rails apps):

```ruby
# app/views/articles/index.json.jbuilder
json.array! @articles do |article|
  json.extract! article, :id, :title, :created_at
  json.url article_url(article, format: :json)
  json.author article.user.name
end
```

**A plain Ruby serializer class**, simple and dependency-free:

```ruby
class ArticleSerializer
  def initialize(article)
    @article = article
  end

  def as_json(*)
    {
      id: @article.id,
      title: @article.title,
      summary: @article.body.to_s.truncate(120),
      published_at: @article.created_at.iso8601
    }
  end
end

render json: ArticleSerializer.new(@article)
render json: @articles.map { |a| ArticleSerializer.new(a) }
```

Popular gems include **Alba**, **Blueprinter**, **Active Model Serializers**, and **JSONAPI::Serializer**. Pick one for consistent, fast output.

Avoid leaking private data (password digests, tokens, internal ids). Prefer explicit allow-lists of fields over exposing everything.

---

## 17.3 Token authentication

APIs usually can't use browser sessions. Clients send a **token** in the `Authorization` header:

```text
Authorization: Bearer <token>
```

Generate a token per user with `has_secure_token`:

```bash
bin/rails generate migration AddApiTokenToUsers api_token:string:uniq
```

```ruby
class User < ApplicationRecord
  has_secure_password
  has_secure_token :api_token      # sets a random token on create
end

user.regenerate_api_token          # rotate it
```

Check it in a base controller:

```ruby
class ApplicationController < ActionController::API
  include ActionController::HttpAuthentication::Token::ControllerMethods

  before_action :authenticate

  private

  def authenticate
    authenticate_or_request_with_http_token do |token, _options|
      @current_user = User.find_by(api_token: token)
    end
  end
end
```

Call it:

```bash
curl http://localhost:3000/articles -H "Authorization: Bearer YOUR_TOKEN"
```

Notes:

- Always use **HTTPS**, since tokens are credentials.
- Give clients a login endpoint that exchanges email and password (`authenticate_by`) for a token.
- For expiring tokens, consider **JWT** (e.g. the `jwt` gem) or store a token with an `expires_at`.
- Never log tokens. Rails' `filter_parameters` setting masks sensitive params in logs.

---

## 17.4 Versioning

Once clients depend on your API, changing responses can break them. **Version** your API so old clients keep working.

**URL versioning** is the most common:

```ruby
# config/routes.rb
namespace :api do
  namespace :v1 do
    resources :articles
  end
  namespace :v2 do
    resources :articles
  end
end
```

```ruby
# app/controllers/api/v1/articles_controller.rb
module Api
  module V1
    class ArticlesController < ApplicationController
      def index
        render json: Article.all.map { |a| ArticleSerializer.new(a) }
      end
    end
  end
end
```

Endpoints become `/api/v1/articles`. Share code between versions through base controllers or concerns, and put separate serializers per version.

**Header versioning** is an alternative using a route constraint:

```ruby
constraints ->(req) { req.headers["Accept"].to_s.include?("version=2") } do
  scope module: :v2 do
    resources :articles
  end
end
```

Good practices:

- Only create a new version for **breaking** changes. Adding fields is usually safe.
- Announce deprecations and give clients time to migrate.
- Add **pagination** (e.g. the `pagy` gem) so big lists don't overload your server.
- Document your API (OpenAPI/Swagger, or `rswag`).

> Official reference: [Using Rails for API-only Applications](https://guides.rubyonrails.org/api_app.html)

---

[Previous](./[16]-Hotwire-Turbo-And-Stimulus.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[18]-Testing-Rails-Applications.md)