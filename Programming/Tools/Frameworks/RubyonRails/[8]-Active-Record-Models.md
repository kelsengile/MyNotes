[Previous](./[7]-Forms-And-Form-Helpers.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[9]-Migrations.md)

*Data*

# Lesson 8 - Active Record Models

## 8.1 Models and tables

**Active Record** is Rails' ORM (Object-Relational Mapper). It maps a database *table* to a Ruby *class* and each *row* to an object.

Generate a model:

```bash
bin/rails generate model Article title:string body:text published:boolean
bin/rails db:migrate
```

This creates a migration (Lesson 9) and the class:

```ruby
# app/models/article.rb
class Article < ApplicationRecord
end
```

That empty class is already fully functional. Rails reads the `articles` table and gives your class an attribute for each column:

```ruby
article = Article.new
article.title = "Hello"
article.body = "First post"
article.title       # => "Hello"
article.attributes  # => { "id" => nil, "title" => "Hello", ... }
```

Things to know:

- Class names are singular (`Article`); table names are plural (`articles`).
- Every table has an auto-incrementing primary key `id`.
- `created_at` and `updated_at` are filled in automatically when the table has them.
- All models inherit from `ApplicationRecord`, so you can add shared behavior there.
- Add your own methods like any Ruby class:

```ruby
class Article < ApplicationRecord
  def summary
    body.to_s.truncate(100)
  end
end
```

---

## 8.2 CRUD operations

**Create**

```ruby
article = Article.new(title: "Hello", body: "World")
article.save                      # => true / false

Article.create(title: "Hi")       # new + save in one step
Article.create!(title: "Hi")      # raises an error if invalid
```

**Read**

```ruby
Article.all                       # all records (a lazy relation)
Article.find(1)                   # by id, raises RecordNotFound if missing
Article.find_by(title: "Hello")   # first match or nil
Article.first
Article.last
Article.count
```

**Update**

```ruby
article = Article.find(1)
article.title = "New title"
article.save

article.update(title: "New title", published: true)   # assign + save
Article.where(published: false).update_all(published: true)  # bulk, skips callbacks/validations
```

**Delete**

```ruby
article.destroy                   # runs callbacks, returns the object
Article.destroy_by(published: false)
Article.where(published: false).delete_all   # fast, skips callbacks
```

`save` and `update` return `true` or `false`. Their bang versions (`save!`, `update!`, `create!`) raise an exception, which is useful in scripts and seeds.

---

## 8.3 Querying with where and order

Active Record builds SQL from chainable methods. Queries are **lazy**: nothing hits the database until you use the result (iterate it, call `.to_a`, `.first`, `.count`, etc.).

```ruby
Article.where(published: true)
Article.where(published: true).order(created_at: :desc).limit(10)
Article.where.not(title: nil)
Article.where(created_at: 1.week.ago..)             # a range
Article.where(id: [1, 2, 3])                        # IN (...)
Article.order(:title)                               # ASC by default
Article.select(:id, :title)                         # only some columns
Article.pluck(:title)                               # array of values, not objects
Article.where(published: true).count
Article.exists?(title: "Hello")
Article.group(:published).count                     # => { true => 5, false => 2 }
Article.offset(20).limit(10)                        # simple pagination
```

**Safe SQL conditions.** When you need raw SQL, use placeholders, never string interpolation, to prevent SQL injection:

```ruby
# GOOD
Article.where("title LIKE ?", "%#{term}%")
Article.where("created_at > :date", date: 1.day.ago)

# BAD: vulnerable to SQL injection
Article.where("title LIKE '%#{params[:q]}%'")
```

Because queries chain, you can build them step by step:

```ruby
articles = Article.all
articles = articles.where(published: true) if params[:only_published]
articles = articles.order(created_at: :desc)
```

Use `.to_sql` in the console to see the generated SQL: `Article.where(published: true).to_sql`.

---

## 8.4 Scopes

A **scope** is a named, reusable query defined on a model:

```ruby
class Article < ApplicationRecord
  scope :published, -> { where(published: true) }
  scope :recent, -> { order(created_at: :desc) }
  scope :written_by, ->(author) { where(author: author) }
end
```

Use them like built-in methods, and chain them:

```ruby
Article.published
Article.published.recent.limit(5)
Article.written_by(user).published
```

Scopes always return a relation, so chaining never breaks (even if a scope's condition is blank).

A class method works the same way and is handy for more complex logic:

```ruby
def self.search(term)
  term.present? ? where("title LIKE ?", "%#{term}%") : all
end
```

**Enums** automatically generate scopes and predicate methods:

```ruby
class Article < ApplicationRecord
  enum :status, { draft: 0, published: 1, archived: 2 }
end

Article.published          # scope
article.draft?             # predicate
article.archived!          # sets status and saves
```

Avoid `default_scope`. It silently changes every query and is surprising to debug. Prefer explicit named scopes.

> Official references: [Active Record Basics](https://guides.rubyonrails.org/active_record_basics.html), [Active Record Query Interface](https://guides.rubyonrails.org/active_record_querying.html)

---

[Previous](./[7]-Forms-And-Form-Helpers.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[9]-Migrations.md)