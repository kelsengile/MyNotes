[Previous](./[8]-Active-Record-Models.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[10]-Associations.md)

*Data*

# Lesson 9 - Migrations

## 9.1 Creating migrations

A **migration** is a Ruby file that describes a change to the database schema. Migrations are version-controlled, repeatable, and shared by the whole team.

Generate one:

```bash
bin/rails generate migration CreateArticles title:string body:text
bin/rails generate migration AddPublishedAtToArticles published_at:datetime
bin/rails generate migration RemoveBodyFromArticles body:text
```

The name tells Rails what to scaffold:

| Name pattern | Generated change |
|---|---|
| `CreateXxx` | `create_table` |
| `AddColumnToTable` | `add_column` |
| `RemoveColumnFromTable` | `remove_column` |

The file goes into `db/migrate/` with a timestamp prefix, such as `20260101120000_create_articles.rb`:

```ruby
class CreateArticles < ActiveRecord::Migration[8.0]
  def change
    create_table :articles do |t|
      t.string :title, null: false
      t.text :body
      t.boolean :published, default: false

      t.timestamps
    end
  end
end
```

The number in `[8.0]` should match your Rails version. Run pending migrations with:

```bash
bin/rails db:migrate
```

Rails records which migrations have run in a `schema_migrations` table, so each runs only once.

**Never edit a migration that has already been shared or deployed.** Write a new one instead.

---

## 9.2 Schema changes

Common operations inside `change`:

```ruby
# tables
create_table :comments do |t|
  t.text :body, null: false
  t.references :article, null: false, foreign_key: true
  t.timestamps
end
drop_table :old_things

# columns
add_column :articles, :slug, :string
remove_column :articles, :slug, :string
rename_column :articles, :body, :content
change_column_default :articles, :published, from: false, to: true
change_column_null :articles, :title, false

# indexes
add_index :articles, :slug, unique: true
add_index :comments, [:article_id, :created_at]

# references (adds article_id + index + optional foreign key)
add_reference :comments, :user, foreign_key: true
```

Common column types: `string`, `text`, `integer`, `bigint`, `float`, `decimal`, `boolean`, `date`, `datetime`, `json`, `references`.

Use `t.decimal :price, precision: 10, scale: 2` for money. Never use `float`.

**`change` vs `up`/`down`.** `change` works when Rails can reverse the operation automatically. For operations it can't reverse, supply both directions:

```ruby
class BackfillSlugs < ActiveRecord::Migration[8.0]
  def up
    Article.find_each { |a| a.update_columns(slug: a.title.parameterize) }
  end

  def down
    # nothing to undo
  end
end
```

Or use `reversible`:

```ruby
def change
  reversible do |dir|
    dir.up   { execute "CREATE VIEW ..." }
    dir.down { execute "DROP VIEW ..." }
  end
end
```

Tip: define lightweight model classes inside data migrations so they keep working even if your real model changes later.

---

## 9.3 Rolling back

Undo changes if you made a mistake:

```bash
bin/rails db:rollback              # undo the last migration
bin/rails db:rollback STEP=3       # undo the last three
bin/rails db:migrate:redo          # rollback then migrate again
bin/rails db:migrate:status        # list up/down status of each migration
bin/rails db:migrate VERSION=20260101120000   # migrate to a specific version
```

Rolling back runs the reverse of `change`, or the `down` method. If a migration is irreversible (for example, it uses `remove_column` without the type), Rails raises `ActiveRecord::IrreversibleMigration`. Fix it by adding the missing information or writing `up`/`down`.

Rollbacks are a **development** tool. In production, fix mistakes by writing a *new* migration that corrects the problem, because rolling back may destroy data.

Reset the development database entirely (drops data!):

```bash
bin/rails db:reset     # drop, create, load schema, run seeds
```

---

## 9.4 Schema.rb and seeds

After each migration, Rails updates `db/schema.rb`, a snapshot of the current database structure:

```ruby
ActiveRecord::Schema[8.0].define(version: 2026_01_01_120000) do
  create_table "articles", force: :cascade do |t|
    t.string "title", null: false
    t.text "body"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end
end
```

- **Commit `schema.rb` to Git.** It is the authoritative structure of your database.
- Don't edit it by hand. Change the schema through migrations.
- New developers (and CI) should load the schema instead of replaying every migration:

```bash
bin/rails db:schema:load
bin/rails db:setup       # create + load schema + seed
bin/rails db:prepare     # create/migrate as needed, seed if the database is new
```

If you use database-specific features (triggers, views), switch to `db/structure.sql` with `config.active_record.schema_format = :sql`.

Rails 8 apps may also have separate schema files for the Solid Queue, Solid Cache, and Solid Cable databases (`queue_schema.rb`, `cache_schema.rb`, and `cable_schema.rb`).

**Seeds** are starter data in `db/seeds.rb`:

```ruby
# db/seeds.rb
User.find_or_create_by!(email_address: "admin@example.com") do |user|
  user.password = "change-me-please"
end

5.times do |i|
  Article.find_or_create_by!(title: "Sample article #{i + 1}") do |a|
    a.body = "This is sample content."
  end
end
```

Using `find_or_create_by!` keeps seeds **idempotent**, meaning you can run them repeatedly without duplicates. Run them with `bin/rails db:seed`.

> Official reference: [Active Record Migrations](https://guides.rubyonrails.org/active_record_migrations.html)

---

[Previous](./[8]-Active-Record-Models.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[10]-Associations.md)