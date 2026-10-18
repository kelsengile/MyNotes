[Previous](./[19]-Security-And-Performance.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[21]-Next-Steps-With-Rails.md)

*Quality And Delivery*

# Lesson 20 - Deployment

**Deployment** means running your app on a server so real users can reach it.

## 20.1 Credentials and environments

Rails runs in an **environment**, set by `RAILS_ENV`:

| Environment | Purpose |
|---|---|
| `development` | Local coding: code reloading, detailed errors |
| `test` | Automated tests |
| `production` | Live site: caching, compiled assets, no debug pages |

Each has a config file: `config/environments/development.rb`, `test.rb`, and `production.rb`. Check the environment in code with `Rails.env.production?`.

**Credentials** are your app's secrets (API keys, SMTP passwords), stored **encrypted** in `config/credentials.yml.enc`. The key to decrypt it is `config/master.key`, which is **never committed to Git** (it's in `.gitignore`).

Edit them:

```bash
bin/rails credentials:edit
```

```yaml
# opened in your editor
aws:
  access_key_id: AKIA...
  secret_access_key: abc123...
smtp:
  password: s3cret
```

Read them in code:

```ruby
Rails.application.credentials.dig(:aws, :access_key_id)
Rails.application.credentials.smtp[:password]
```

On the production server, supply the key as an environment variable instead of a file:

```bash
RAILS_MASTER_KEY=<contents of config/master.key>
```

Per-environment credentials are available too: `bin/rails credentials:edit --environment production`.

For simple settings, plain environment variables work:

```ruby
ENV.fetch("DATABASE_URL")
ENV.fetch("WEB_CONCURRENCY", 2)
```

`ENV.fetch` raises an error when a required variable is missing, which makes mistakes obvious.

---

## 20.2 Kamal and Docker

Rails 8 apps come with a **Dockerfile** and **Kamal**, a tool that deploys your containerized app to any server you can SSH into (a VPS from any provider, or your own machine).

What's included in a new app:

- `Dockerfile`: builds a production image (also used by many hosting platforms).
- `config/deploy.yml`: Kamal's configuration.
- **Thruster**: a small proxy in front of Puma that handles compression, caching, and HTTP/2 features.
- `.kamal/secrets`: where deployment secrets are read from.

A trimmed `config/deploy.yml`:

```yaml
service: myapp
image: your-dockerhub-user/myapp

servers:
  web:
    - 203.0.113.10

proxy:
  ssl: true
  host: myapp.example.com

registry:
  username: your-dockerhub-user
  password:
    - KAMAL_REGISTRY_PASSWORD

env:
  secret:
    - RAILS_MASTER_KEY
  clear:
    SOLID_QUEUE_IN_PUMA: true

volumes:
  - "myapp_storage:/rails/storage"
```

Typical flow:

```bash
bin/kamal setup      # first time: installs Docker on the server, boots the app, gets a TLS certificate
bin/kamal deploy     # build, push, and release a new version with zero downtime
bin/kamal logs       # view logs
bin/kamal console    # open a Rails console on the server
```

What it does for you: builds your image, pushes it to a registry, pulls it on the server, starts the new container, health-checks it, and switches traffic over using `kamal-proxy`, then stops the old one.

Don't forget **persistent storage**: the container's filesystem is replaced on every deploy, so mount a volume (as above) for SQLite databases and Active Storage uploads, or use an external database and S3-style storage.

---

## 20.3 Platform hosting

If you'd rather not manage servers, use a **platform-as-a-service (PaaS)**. You push code, and the platform builds and runs it.

Popular options include **Render**, **Fly.io**, **Heroku**, and **Railway**. Features and pricing change often, so check each provider's current documentation.

What most platforms need from you:

1. **A Git repository** (usually deploying on push to a branch).
2. **A production database.** Use PostgreSQL (`gem "pg"`), configured through a `DATABASE_URL` environment variable, as platform disks may not be persistent.
3. **Environment variables**: at least `RAILS_MASTER_KEY` and `RAILS_ENV=production`.
4. **A build step** (compile assets) and a **release step** (run migrations):

```bash
bin/rails assets:precompile
bin/rails db:migrate        # run on every release
```

5. **A