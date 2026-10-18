[Previous](./[12]-Authentication.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[14]-Active-Storage-And-Action-Mailer.md)

*Application Features*

# Lesson 13 - Authorization

**Authorization** decides what a signed-in user is *allowed* to do: edit this article, view that report, open the admin area.

## 13.1 Roles

The simplest model is a **role** stored on the user. Use an enum:

```bash
bin/rails generate migration AddRoleToUsers role:integer
```

```ruby
# migration: add_column :users, :role, :integer, default: 0, null: false
class User < ApplicationRecord
  enum :role, { member: 0, editor: 1, admin: 2 }
end
```

You automatically get predicates, scopes, and setters:

```ruby
user.admin?         # => false
user.editor!        # sets role and saves
User.admins         # scope
```

For more flexibility you can add a `roles` table with a many-to-many join, so users can hold several roles. Start simple and grow only if needed.

Keep the logic for "who can do what" in one place, either methods on the model or a policy (13.2), rather than scattering `if user.admin?` checks through views.

---

## 13.2 Pundit and CanCanCan

Two popular gems organize authorization rules.

**Pundit**: one *policy class* per model, plain Ruby.

```bash
bundle add pundit
bin/rails generate pundit:install
```

```ruby
# app/policies/article_policy.rb
class ArticlePolicy < ApplicationPolicy
  def update?
    user.admin? || record.user_id == user.id
  end

  def destroy?
    user.admin?
  end

  class Scope < ApplicationPolicy::Scope
    def resolve
      user.admin? ? scope.all : scope.where(user_id: user.id)
    end
  end
end
```

```ruby
class ApplicationController < ActionController::Base
  include Pundit::Authorization
  rescue_from Pundit::NotAuthorizedError, with: :forbidden

  private

  def forbidden
    redirect_to root_path, alert: "You are not allowed to do that."
  end
end

class ArticlesController < ApplicationController
  def update
    @article = Article.find(params[:id])
    authorize @article                       # calls ArticlePolicy#update?
    # ...
  end

  def index
    @articles = policy_scope(Article)        # filtered by Scope
  end
end
```

In views: `<% if policy(@article).update? %> ... <% end %>`.

**CanCanCan**: all rules in one `Ability` class.

```ruby
# app/models/ability.rb
class Ability
  include CanCan::Ability

  def initialize(user)
    can :read, Article
    return unless user

    can :create, Article
    can [:update, :destroy], Article, user_id: user.id
    can :manage, :all if user.admin?
  end
end
```

```ruby
class ArticlesController < ApplicationController
  load_and_authorize_resource
end
```

```erb
<% if can? :update, @article %> ... <% end %>
```

Pundit is explicit and easy to test. CanCanCan is more concise but more "magic". Either is fine for most apps.

---

## 13.3 Securing controllers

Rules for safe controllers:

1. **Authenticate first**, then authorize.
2. **Scope lookups to the current user** so users can't reach others' records by guessing ids:

```ruby
# Risky: any id works
@article = Article.find(params[:id])

# Safer: only this user's articles
@article = Current.user.articles.find(params[:id])
```

If the record isn't theirs, `find` raises `RecordNotFound`, which becomes a 404, and that doesn't reveal that the record exists.

3. **Authorize every action.** Pundit can enforce this:

```ruby
after_action :verify_authorized, except: :index
after_action :verify_policy_scoped, only: :index
```

4. **Return the right status:** 401 for "not signed in", 403 for "signed in but not allowed", and 404 when you'd rather hide existence.

5. **Permit params carefully.** Don't allow users to set fields like `role` or `admin`:

```ruby
params.expect(user: [:name, :email_address])   # never :role from a normal form
```

6. **Use a global handler:**

```ruby
rescue_from ActiveRecord::RecordNotFound do
  render file: Rails.public_path.join("404.html"), status: :not_found, layout: false
end
```

---

## 13.4 Admin areas

Put admin features under a namespace with their own base controller so one guard covers everything:

```ruby
# config/routes.rb
namespace :admin do
  root "dashboard#index"
  resources :users
  resources :articles
end
```

```ruby
# app/controllers/admin/base_controller.rb
module Admin
  class BaseController < ApplicationController
    layout "admin"
    before_action :require_admin

    private

    def require_admin
      redirect_to root_path, alert: "Not authorized." unless Current.user&.admin?
    end
  end
end

# app/controllers/admin/users_controller.rb
module Admin
  class UsersController < BaseController
    def index
      @users = User.order(:email_address)
    end
  end
end
```

Remember:

- **Hiding a link is not security.** Always enforce the check on the server in the controller, too.
- Log admin actions (who changed what) for auditing.
- Consider extra protection for admins, such as two-factor authentication and shorter sessions.
- Gems like **Avo**, **Administrate**, and **ActiveAdmin** can generate admin UIs if you don't want to build your own.

> Official references: [Pundit](https://github.com/varvet/pundit), [CanCanCan](https://github.com/CanCanCommunity/cancancan)

---

[Previous](./[12]-Authentication.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[14]-Active-Storage-And-Action-Mailer.md)