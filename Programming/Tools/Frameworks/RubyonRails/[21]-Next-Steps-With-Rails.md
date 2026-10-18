[Previous](./[20]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md)

*Quality And Delivery*

# Lesson 21 - Next Steps With Rails

## 21.1 Engines and modularity

An **engine** is a miniature Rails application that you plug into another one. It has its own models, controllers, views, and routes. Many gems you've seen (Devise, Active Storage, Action Mailbox) are engines. In fact, a Rails app itself is an engine.

Create a mountable engine:

```bash
rails plugin new blorgh --mountable
```

It lives in its own namespace (`Blorgh::Article`), keeping its code and tables separate. Mount it in the host app:

```ruby
# config/routes.rb of the host app
mount Blorgh::Engine, at: "/blog"
```

Use cases: a reusable admin panel, a shared billing feature across apps, or splitting a large app into clear parts.

You can improve modularity without engines:

- **Concerns** share behavior between models or controllers (Lesson 2).
- **Plain Ruby objects (POROs)** in `app/models` or `app/services` hold a single piece of business logic:

```ruby
class PublishArticle
  def initialize(article, notifier: ArticleNotifier)
    @article = article
    @notifier = notifier
  end

  def call
    @article.update!(published: true, published_at: Time.current)
    @notifier.notify_subscribers(@article)
  end
end

PublishArticle.new(article).call
```

- A **modular monolith**: one deployable app organized into well-bounded domains (tools like Packwerk help enforce boundaries).

Start simple. Extract code into engines or services when the pain is real, not just in case.

---

## 21.2 Real-time with Action Cable

**Action Cable** integrates WebSockets into Rails, so the server can push updates to browsers instantly (chat, notifications, live dashboards). Turbo Streams broadcasts (Lesson 16) are built on it.

Generate a channel:

```bash
bin/rails generate channel Chat
```

Server side:

```ruby
# app/channels/chat_channel.rb
class ChatChannel < ApplicationCable::Channel
  def subscribed
    stream_from "chat_room"
  end

  def speak(data)
    ActionCable.server.broadcast("chat_room", { message: data["message"] })
  end
end
```

Identify the connected user in `ApplicationCable::Connection` (using your authentication, for example the session cookie) and reject unauthorized connections.

Client side:

```js
// app/javascript/channels/chat_channel.js
import consumer from "channels/consumer"

const chat = consumer.subscriptions.create("ChatChannel", {
  received(data) {
    document.getElementById("messages").insertAdjacentHTML("beforeend", `<p>${data.message}</p>`)
  }
})

// chat.perform("speak", { message: "Hello" })
```

Broadcast from anywhere (a model, controller, or job):

```ruby
ActionCable.server.broadcast("chat_room", { message: "Server announcement" })
```

The **subscription adapter** is configured in `config/cable.yml`:

| Adapter | Notes |
|---|---|
| `async` | In-process; development only |
| `solid_cable` | Database-backed; the Rails 8 default, no Redis needed |
| `redis` | Redis-backed; widely used at scale |

Be careful when inserting received text into the page (as in the example above). Escape user content to avoid XSS, or broadcast pre-rendered HTML partials via Turbo Streams.

---

## 21.3 Rails ecosystem

You've covered the core. Here's where to keep learning.

**Official resources**

- [Rails Guides](https://guides.rubyonrails.org/): the best reference for every topic in this Topic
- [Rails API docs](https://api.rubyonrails.org/): class and method documentation
- [rubyonrails.org](https://rubyonrails.org/): news and release announcements
- [Ruby documentation](https://www.ruby-lang.org/en/documentation/)
- [Turbo](https://turbo.hotwired.dev/) and [Stimulus](https://stimulus.hotwired.dev/) handbooks

**Commonly used gems** (check each project's maintenance status before adopting):

| Need | Gems |
|---|---|
| Authentication | Devise, Rails' built-in generator |
| Authorization | Pundit, CanCanCan |
| Background jobs | Solid Queue, Sidekiq, GoodJob |
| Testing | RSpec, FactoryBot, Capybara, Faker |
| Pagination | Pagy, Kaminari |
| Search and filtering | Ransack, pg_search |
| Forms | simple_form |
| File processing | image_processing |
| Quality | RuboCop, Brakeman, Bullet |
| Admin interfaces | Avo, Administrate, ActiveAdmin |

**Community**

- Rails conferences (RailsConf, Rails World) and local Ruby meetups
- Newsletters such as Ruby Weekly
- The Rails and Ruby communities on GitHub, forums, and chat servers
- Reading the source code of Rails itself, which is surprisingly readable and a great way to deepen your understanding

**Keep your app current.** Rails releases regularly; upgrade steadily using the official upgrade guides and `bin/rails app:update`, instead of waiting years.

---

## 21.4 Project ideas

Building projects is the best way to cement what you've learned. Each idea below lists the lessons it exercises.

| Project | Practices |
|---|---|
| **Personal blog** with posts, comments, tags | Routing, controllers, associations (has_many through), forms, validations, Turbo Streams for comments |
| **Task / to-do tracker** with projects and due dates | Active Record queries and scopes, enums, authentication, authorization (each user sees only their tasks) |
| **URL shortener** with click statistics | Routing, custom redirects, indexes, caching, background jobs for analytics |
| **Mini online store** (catalog, cart, orders) | Associations, migrations, file uploads, mailers, background jobs, testing |
| **Real-time chat room** | Hotwire, Action Cable, authentication, broadcasts |
| **JSON API for a mobile or JS app** | API mode, serializers, token auth, versioning, testing |
| **Recipe or book collection** with search and images | Active Storage variants, pagination, search, system tests |
| **Team feedback or poll app** | Roles, admin area, validations, deployment with Kamal |

Suggested approach for any project:

1. Sketch the data model (tables and relationships) on paper first.
2. Build the smallest working version (one resource, one page).
3. Write a few tests for each feature as you add it.
4. Add authentication and authorization early, not at the end.
5. Deploy a first version quickly (Lesson 20), then keep improving.
6. Refactor and review your code with the Rails Guides beside you.

You now have a full path from `rails new` to a deployed, tested, secure application. Keep building, and keep reading other people's Rails code.

> Official references: [Getting Started with Engines](https://guides.rubyonrails.org/engines.html), [Action Cable Overview](https://guides.rubyonrails.org/action_cable_overview.html)

---

[Previous](./[20]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md)