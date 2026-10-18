[Previous](./[11]-Validations-And-Callbacks.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[13]-Authorization.md)

*Application Features*

# Lesson 12 - Authentication

**Authentication** answers "who are you?". (Lesson 13 covers authorization, which answers "what may you do?".)

## 12.1 has_secure_password

Never store passwords as plain text. `has_secure_password` stores a salted **bcrypt hash** in a `password_digest` column.

Setup:

```bash
bundle add bcrypt
bin/rails generate model User email_address:string password_digest:string
bin/rails db:migrate
```

```ruby
class User < ApplicationRecord
  has_secure_password

  normalizes :email_address, with: ->(e) { e.strip.downcase }
  validates :email_address, presence: true, uniqueness: true
end
```

What you get:

```ruby
user = User.create!(email_address: "ada@example.com",
                    password: "s3cret-pass", password_confirmation: "s3cret-pass")

user.authenticate("wrong")        # => false
user.authenticate("s3cret-pass")  # => user
User.authenticate_by(email_address: "ada@example.com", password: "s3cret-pass")
```

- `authenticate_by` returns the user or `nil`, and is safer against timing attacks than finding the user first.
- The virtual `password` and `password_confirmation` attributes are validated automatically. Passwords longer than 72 bytes are not allowed by bcrypt.
- Only `password_digest` is saved. The plain password is never stored.

For password-reset links, `generates_token_for` creates signed, expiring tokens:

```ruby
generates_token_for :password_reset, expires_in: 15.minutes do
  password_salt&.last(10)   # token becomes invalid once the password changes
end

token = user.generate_token_for(:password_reset)
User.find_by_token_for(:password_reset, token)
```

---

## 12.2 Sessions

HTTP is stateless, so a **session** remembers who is logged in between requests. Rails stores session data in an encrypted, signed cookie.

```ruby
session[:user_id] = user.id    # log in
session.delete(:user_id)       # log out
reset_session                  # clear everything (do this on login to prevent fixation)
```

A hand-rolled login flow:

```ruby
# config/routes.rb
resource :session, only: [:new, :create, :destroy]
```

```ruby
class SessionsController < ApplicationController
  def new; end

  def create
    user = User.authenticate_by(params.permit(:email_address, :password))
    if user
      reset_session
      session[:user_id] = user.id
      redirect_to root_path, notice: "Signed in."
    else
      flash.now[:alert] = "Invalid email or password."
      render :new, status: :unprocessable_entity
    end
  end

  def destroy
    reset_session
    redirect_to root_path, notice: "Signed out."
  end
end
```

Share the logged-in user across controllers and views:

```ruby
class ApplicationController < ActionController::Base
  helper_method :current_user

  private

  def current_user
    @current_user ||= User.find_by(id: session[:user_id])
  end

  def require_login
    redirect_to new_session_path, alert: "Please sign in." unless current_user
  end
end
```

```ruby
before_action :require_login, except: [:index, :show]
```

Always serve a login form over HTTPS (Lesson 19) so credentials and cookies can't be intercepted.

---

## 12.3 Rails built-in authentication generator

Rails 8 includes a generator that creates a basic, production-quality authentication system you own and can read:

```bash
bin/rails generate authentication
bin/rails db:migrate
```

It creates, among other things:

| Piece | Role |
|---|---|
| `User` model | `has_secure_password`, normalized `email_address` |
| `Session` model | One row per signed-in device (user, IP address, user agent) |
| `Current` class | Holds the current session/user for the request |
| `Authentication` concern | Included in `ApplicationController` |
| `SessionsController` and views | Sign in and sign out |
| `PasswordsController` + mailer | Password reset by email |

By default, **every action requires a signed-in user**. Opt out per controller:

```ruby
class ArticlesController < ApplicationController
  allow_unauthenticated_access only: [:index, :show]
end
```

Access the signed-in user anywhere in the request:

```ruby
Current.user
Current.session
```

Inside the concern you'll find methods such as `require_authentication`, `start_new_session_for(user)`, and `terminate_session`. The session id is stored in a signed, permanent, HTTP-only cookie.

Important: the generator does **not** create a sign-up page. You add registration yourself (a `UsersController#new/create` that builds a `User`). Treat the generated code as a starting point and customize it.

---

## 12.4 Devise overview

**Devise** is the most popular authentication gem. It provides many ready-made features through *modules*.

Install:

```bash
bundle add devise
bin/rails generate devise:install
bin/rails generate devise User
bin/rails db:migrate
```

Modules you can switch on in the `User` model:

```ruby
class User < ApplicationRecord
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable
         # optional: :confirmable, :lockable, :trackable, :timeoutable, :omniauthable
end
```

| Module | Feature |
|---|---|
| `database_authenticatable` | Password login |
| `registerable` | Sign-up, edit account |
| `recoverable` | Password reset emails |
| `rememberable` | "Remember me" cookie |
| `validatable` | Email/password validations |
| `confirmable` | Confirm email address |
| `lockable` | Lock account after failed attempts |
| `omniauthable` | Login with Google, GitHub, etc. |

Use it in controllers and views:

```ruby
before_action :authenticate_user!
current_user
user_signed_in?
```

```erb
<% if user_signed_in? %>
  <%= button_to "Sign out", destroy_user_session_path, method: :delete %>
<% else %>
  <%= link_to "Sign in", new_user_session_path %>
<% end %>
```

Devise is feature-rich, but it hides a lot behind the gem. Choose the built-in generator for simple apps, and Devise when you need many features (confirmation, lockout, OAuth) quickly.

> Official references: [Securing Rails Applications](https://guides.rubyonrails.org/security.html), [Rails 8.0 Release Notes](https://guides.rubyonrails.org/8_0_release_notes.html), [Devise on GitHub](https://github.com/heartcombo/devise)

---

[Previous](./[11]-Validations-And-Callbacks.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[13]-Authorization.md)