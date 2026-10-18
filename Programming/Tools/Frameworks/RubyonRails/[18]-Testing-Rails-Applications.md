[Previous](./[17]-Building-APIs-With-Rails.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[19]-Security-And-Performance.md)

*Quality And Delivery*

# Lesson 18 - Testing Rails Applications

Automated tests check that your code works, and keep it working as you change it. Rails sets up testing from the first `rails new`.

## 18.1 Minitest and RSpec

Rails uses **Minitest** by default. The `test/` folder mirrors your app:

```text
test/
├── fixtures/          sample data (YAML)
├── models/
├── controllers/
├── integration/
├── mailers/
├── system/            browser tests
└── test_helper.rb
```

Run tests:

```bash
bin/rails test                      # all tests (except system)
bin/rails test test/models          # one folder
bin/rails test test/models/article_test.rb:12   # one test by line
bin/rails test:system               # browser tests
```

A basic test:

```ruby
require "test_helper"

class ArticleTest < ActiveSupport::TestCase
  test "is invalid without a title" do
    article = Article.new(body: "No title")
    assert_not article.valid?
    assert_includes article.errors[:title], "can't be blank"
  end
end
```

Common assertions: `assert`, `assert_not`, `assert_equal expected, actual`, `assert_nil`, `assert_includes`, `assert_raises`, `assert_difference`.

**RSpec** is a popular alternative with a descriptive syntax:

```bash
bundle add rspec-rails --group "development,test"
bin/rails generate rspec:install
bundle exec rspec
```

```ruby
# spec/models/article_spec.rb
require "rails_helper"

RSpec.describe Article, type: :model do
  it "is invalid without a title" do
    article = Article.new(body: "No title")
    expect(article).not_to be_valid
    expect(article.errors[:title]).to include("can't be blank")
  end
end
```

Both work well. Minitest is simpler and ships with Rails, and RSpec has a large ecosystem and expressive style. Pick one per project and stay consistent.

---

## 18.2 Model and controller tests

**Model tests** check validations, scopes, associations, and methods:

```ruby
class ArticleTest < ActiveSupport::TestCase
  test "published scope returns only published articles" do
    assert_includes Article.published, articles(:published_one)
    assert_not_includes Article.published, articles(:draft_one)
  end

  test "summary is truncated" do
    article = Article.new(body: "a" * 500)
    assert article.summary.length <= 100
  end
end
```

**Controller tests** are *integration tests* that send real requests through routes, controllers, and views:

```ruby
require "test_helper"

class ArticlesControllerTest < ActionDispatch::IntegrationTest
  test "index renders" do
    get articles_url
    assert_response :success
    assert_select "h1", "Articles"
  end

  test "create makes an article and redirects" do
    assert_difference("Article.count", 1) do
      post articles_url, params: { article: { title: "Hi", body: "There" } }
    end
    assert_redirected_to article_url(Article.last)
  end

  test "create with invalid data re-renders the form" do
    assert_no_difference("Article.count") do
      post articles_url, params: { article: { title: "" } }
    end
    assert_response :unprocessable_entity
  end

  test "update changes the title" do
    article = articles(:one)
    patch article_url(article), params: { article: { title: "Updated" } }
    assert_redirected_to article_url(article)
    assert_equal "Updated", article.reload.title
  end
end
```

Helpers to know:

- `get`, `post`, `patch`, `delete` send requests.
- `follow_redirect!` follows a redirect.
- `assert_select` checks HTML content.
- `assert_enqueued_jobs` checks background jobs.
- For authenticated routes, sign in first by posting to your login route in a `setup` block.

---

## 18.3 System tests

**System tests** drive a real browser: they click, type, and read the page like a user. They are slower but test the whole stack, including JavaScript.

```ruby
# test/application_system_test_case.rb
require "test_helper"

class ApplicationSystemTestCase < ActionDispatch::SystemTestCase
  driven_by :selenium, using: :headless_chrome, screen_size: [1400, 1400]
end
```

```ruby
# test/system/articles_test.rb
require "application_system_test_case"

class ArticlesTest < ApplicationSystemTestCase
  test "creating an article" do
    visit articles_url
    click_on "New article"

    fill_in "Title", with: "System test post"
    fill_in "Body", with: "Written by a robot."
    click_on "Create Article"

    assert_text "Article created"
    assert_text "System test post"
  end
end
```

Common Capybara actions: `visit`, `click_on`, `click_button`, `fill_in`, `select`, `check`, `attach_file`, `assert_text`, `assert_selector`, `accept_confirm { ... }`.

Capybara waits automatically for elements to appear, so avoid `sleep`. Run with `bin/rails test:system`. Failing tests save a screenshot to `tmp/screenshots`. You need Chrome (or another browser) installed.

Keep system tests for important user journeys (sign-up, checkout), and cover the details with faster model and controller tests.

---

## 18.4 Fixtures and factories

Tests need data. Rails offers two approaches.

**Fixtures** are YAML files that are loaded into the test database before the tests run:

```yaml
# test/fixtures/articles.yml
one:
  title: First article
  body: Hello
  published: true

draft_one:
  title: A draft
  body: Not yet
  published: false
```

```ruby
articles(:one)             # returns the Article
articles(:one).title       # => "First article"
```

Fixtures can reference each other by label (`user: ada`) and use ERB (`created_at: <%= 2.days.ago %>`). They're very fast, and shared across all tests.

**Factories** (the **FactoryBot** gem) build records in code, which keeps each test's data explicit:

```ruby
# Gemfile: gem "factory_bot_rails", group: [:development, :test]

# spec/factories/articles.rb  (or test/factories/)
FactoryBot.define do
  factory :article do
    sequence(:title) { |n| "Article #{n}" }
    body { "Some content" }
    published { false }

    trait :published do
      published { true }
    end
  end
end
```

```ruby
build(:article)                      # unsaved
create(:article, :published)         # saved
create_list(:article, 3)
attributes_for(:article)             # hash for params
```

Comparison:

| | Fixtures | Factories |
|---|---|---|
| Speed | Faster | Slower (inserts per test) |
| Visibility | Data lives in separate files | Data defined near the test |
| Flexibility | Static | Dynamic, with traits |

To use FactoryBot with Minitest, add `include FactoryBot::Syntax::Methods` to `ActiveSupport::TestCase`.

Good testing habits: test behavior (not implementation details), keep tests independent, name tests clearly, and run them in CI on every push.

> Official reference: [Testing Rails Applications](https://guides.rubyonrails.org/testing.html)

---

[Previous](./[17]-Building-APIs-With-Rails.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[19]-Security-And-Performance.md)