[Previous](./[10]-Associations.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[12]-Authentication.md)

*Data*

# Lesson 11 - Validations And Callbacks

## 11.1 Built-in validations

**Validations** make sure only valid data is saved. They run on `save`, `create`, and `update`.

```ruby
class User < ApplicationRecord
  validates :name, presence: true
  validates :email_address, presence: true,
                            uniqueness: { case_sensitive: false },
                            format: { with: URI::MailTo::EMAIL_REGEXP }
  validates :age, numericality: { only_integer: true, greater_than_or_equal_to: 18 }
  validates :username, length: { in: 3..20 }
  validates :role, inclusion: { in: %w[member admin] }
  validates :terms, acceptance: true
  validates :password, confirmation: true
end
```

Common helpers:

| Validator | Checks |
|---|---|
| `presence` | Not blank |
| `uniqueness` | No other record has the same value |
| `length` | `minimum`, `maximum`, `in`, `is` |
| `format` | Matches a regular expression |
| `numericality` | Is a number (with limits) |
| `inclusion` / `exclusion` | In / not in a list |
| `acceptance` | Checkbox was ticked |
| `confirmation` | A `field_confirmation` matches `field` |

Useful options: `allow_nil: true`, `allow_blank: true`, `on: :create`, `if: :method` and `unless: -> { ... }`.

Checking validity:

```ruby
user = User.new
user.valid?              # => false
user.errors.full_messages # => ["Name can't be blank", ...]
user.save                # => false, nothing written
```

**Important:** a `uniqueness` validation can still race under concurrent requests. Always back it up with a unique database index (Lesson 9).

---

## 11.2 Custom validations

**A custom method** is the simplest approach. Register it with `validate` and add messages with `errors.add`:

```ruby
class Event < ApplicationRecord
  validate :starts_in_the_future

  private

  def starts_in_the_future
    return if starts_at.blank?
    errors.add(:starts_at, "must be in the future") if starts_at <= Time.current
  end
end
```

Use `:base` for errors that don't belong to one field:

```ruby
errors.add(:base, "An event cannot overlap another event")
```

**A reusable validator** for one attribute:

```ruby
# app/validators/email_validator.rb
class EmailValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    unless value =~ URI::MailTo::EMAIL_REGEXP
      record.errors.add(attribute, options[:message] || "is not a valid email")
    end
  end
end

class User < ApplicationRecord
  validates :email_address, email: true
end
```

The class name `EmailValidator` maps to the `email:` option.

For validating many fields with one class, use `validates_with` and a class that inherits from `ActiveModel::Validator`.

---

## 11.3 Callbacks

**Callbacks** are hooks that run at specific moments in a model's life.

```ruby
class User < ApplicationRecord
  before_validation :normalize_email
  before_save :set_defaults
  after_create :send_welcome_email
  after_commit :sync_to_crm, on: :create

  private

  def normalize_email
    self.email_address = email_address.to_s.strip.downcase
  end

  def set_defaults
    self.role ||= "member"
  end

  def send_welcome_email
    UserMailer.welcome(self).deliver_later
  end

  def sync_to_crm
    CrmSyncJob.perform_later(id)
  end
end
```

Order of the main ones when creating a record:

1. `before_validation`
2. `after_validation`
3. `before_save`
4. `before_create`
5. *(row inserted)*
6. `after_create`
7. `after_save`
8. `after_commit` (after the transaction is committed)

Updates run `before_update` and `after_update` in place of the `create` ones; deletes run `before_destroy` and `after_destroy`.

Guidelines:

- Use callbacks for **data housekeeping** (normalizing, defaults).
- For side effects that touch outside systems (emails, API calls), use `after_commit` and enqueue a background job, so a rolled-back transaction doesn't trigger them.
- Don't hide complex business flows in callbacks. They make code hard to follow and test. Consider a service object or explicit method instead.
- Returning from a `before_*` callback doesn't cancel it. Use `throw :abort` to halt the chain.

---

## 11.4 Error messages

Every record has an `errors` object that is filled when validation fails.

```ruby
user.errors.full_messages     # => ["Name can't be blank"]
user.errors[:name]            # => ["can't be blank"]
user.errors.where(:name).any? # => true
user.errors.count
```

**Showing them in a form** (see Lesson 7):

```erb
<% if @user.errors.any? %>
  <ul>
    <% @user.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
  </ul>
<% end %>
```

Show per-field messages next to an input with `@user.errors[:email_address].first`.

**Custom messages** per validation:

```ruby
validates :name, presence: { message: "is required" }
validates :age, numericality: { greater_than: 17, message: "must be 18 or older" }
```

**Translate (i18n) messages** in `config/locales/en.yml`:

```yaml
en:
  activerecord:
    errors:
      models:
        user:
          attributes:
            name:
              blank: "is required, please fill it in"
    attributes:
      user:
        email_address: "Email"
```

The second block renames the attribute in full messages ("Email is invalid" instead of "Email address is invalid").

Remember to render the form again with `status: :unprocessable_entity` after a failed save, so the errors display.

> Official references: [Active Record Validations](https://guides.rubyonrails.org/active_record_validations.html), [Active Record Callbacks](https://guides.rubyonrails.org/active_record_callbacks.html)

---

[Previous](./[10]-Associations.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[12]-Authentication.md)