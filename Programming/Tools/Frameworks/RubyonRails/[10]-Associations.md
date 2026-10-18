[Previous](./[9]-Migrations.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[11]-Validations-And-Callbacks.md)

*Data*

# Lesson 10 - Associations

## 10.1 belongs_to and has_many

Associations describe how models relate. The most common pair is **one-to-many**: an article *has many* comments, and each comment *belongs to* an article.

The foreign key lives on the "belongs to" side:

```bash
bin/rails generate model Comment body:text article:references
bin/rails db:migrate
```

```ruby
class Article < ApplicationRecord
  has_many :comments, dependent: :destroy
end

class Comment < ApplicationRecord
  belongs_to :article
end
```

You now get methods for free:

```ruby
article = Article.find(1)
article.comments                       # all its comments
article.comments.create(body: "Nice!") # creates with article_id set
article.comments.build(body: "Draft")  # new, unsaved
article.comments.count
comment.article                        # the parent
```

Notes:

- `belongs_to` is **required by default**: saving a comment without an article fails validation. Use `belongs_to :article, optional: true` to allow it.
- `dependent: :destroy` deletes the comments when the article is destroyed. Other options include `:delete_all`, `:nullify`, and `:restrict_with_error`.
- Also available: `has_one` for one-to-one relations (a user has one profile; the profile belongs to the user).

```ruby
class User < ApplicationRecord
  has_one :profile, dependent: :destroy
end
```

---

## 10.2 has_many through

**Many-to-many** relations need a join model that stores the pairing. `has_many :through` is the recommended approach because the join model can hold its own data and logic.

Example: articles and tags.

```bash
bin/rails generate model Tag name:string
bin/rails generate model Tagging article:references tag:references
bin/rails db:migrate
```

```ruby
class Article < ApplicationRecord
  has_many :taggings, dependent: :destroy
  has_many :tags, through: :taggings
end

class Tag < ApplicationRecord
  has_many :taggings, dependent: :destroy
  has_many :articles, through: :taggings
end

class Tagging < ApplicationRecord
  belongs_to :article
  belongs_to :tag
end
```

Usage:

```ruby
article.tags << Tag.find_or_create_by(name: "rails")
article.tags.map(&:name)           # => ["rails"]
Tag.find_by(name: "rails").articles
article.tag_ids = [1, 2, 3]        # replaces the set
```

`has_many :through` also works as a shortcut across a chain, for example `has_many :commenters, through: :comments, source: :user`.

Add a unique index on the join table so the same pair can't be saved twice:

```ruby
add_index :taggings, [:article_id, :tag_id], unique: true
```

---

## 10.3 Polymorphic associations

A **polymorphic** association lets one model belong to *more than one* other model through a single association. For example, a `Comment` can belong to an `Article` *or* a `Photo`.

Migration:

```ruby
create_table :comments do |t|
  t.text :body
  t.references :commentable, polymorphic: true, null: false
  t.timestamps
end
```

This creates two columns, `commentable_id` and `commentable_type`, plus an index.

Models:

```ruby
class Comment < ApplicationRecord
  belongs_to :commentable, polymorphic: true
end

class Article < ApplicationRecord
  has_many :comments, as: :commentable, dependent: :destroy
end

class Photo < ApplicationRecord
  has_many :comments, as: :commentable, dependent: :destroy
end
```

Usage:

```ruby
article.comments.create(body: "Great read")
photo.comments.create(body: "Nice shot")
comment.commentable    # returns the Article or the Photo
```

Trade-off: the database can't enforce a foreign key across several tables, so integrity is maintained only in Rails. Use polymorphism for genuinely shared behavior (comments, attachments, likes, tags), not as a default.

---

## 10.4 Avoiding N+1 queries

The **N+1 problem** happens when you load a list (1 query) and then query once more for each item (N queries):

```ruby
@articles = Article.all
```

```erb
<% @articles.each do |article| %>
  <%= article.comments.size %>   <%# one query PER article! %>
<% end %>
```

With 100 articles that's 101 queries. Fix it with **eager loading**:

```ruby
@articles = Article.includes(:comments)
```

Now Rails runs 2 queries total.

| Method | Behavior |
|---|---|
| `includes(:comments)` | Smart default; picks `preload` or `eager_load` |
| `preload(:comments)` | Always separate queries |
| `eager_load(:comments)` | Single query with `LEFT OUTER JOIN` (needed when filtering on the association) |

Nest them when needed: `Article.includes(comments: :user)`.

**Counter caches** avoid counting rows at all:

```ruby
class Comment < ApplicationRecord
  belongs_to :article, counter_cache: true
end
# needs an integer column: articles.comments_count (default 0)
```

Then `article.comments.size` reads the cached column.

**Spotting N+1:**

- Watch the development log for repeated, nearly identical queries.
- Use the `bullet` gem in development to flag them.
- Call `strict_loading` on a relation (`Article.strict_loading`) to raise an error whenever an association is lazily loaded.

> Official reference: [Active Record Associations](https://guides.rubyonrails.org/association_basics.html)

---

[Previous](./[9]-Migrations.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[11]-Validations-And-Callbacks.md)