[Previous](./[13]-Authorization.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[15]-Background-Jobs-With-Active-Job.md)

*Application Features*

# Lesson 14 - Active Storage And Action Mailer

## 14.1 Uploading files

**Active Storage** attaches files (images, PDFs, videos) to models and stores them on disk or in the cloud.

One-time setup (new apps usually have this already):

```bash
bin/rails active_storage:install
bin/rails db:migrate
```

This creates tables that track blobs (the files) and attachments (links between files and records). Your own tables need no extra columns.

Declare attachments:

```ruby
class Article < ApplicationRecord
  has_one_attached :cover
  has_many_attached :photos
end
```

Attach, check, and remove:

```ruby
article.cover.attach(params[:cover])
article.cover.attached?        # => true
article.cover.filename         # => "photo.jpg"
article.cover.byte_size
article.cover.purge            # delete file and record
article.cover.purge_later      # delete in a background job
article.photos.attach(io: File.open("pic.png"), filename: "pic.png")
```

In a form (Lesson 7) use `form.file_field :cover`. Then display it:

```erb
<% if @article.cover.attached? %>
  <%= image_tag @article.cover %>
<% end %>
<%= link_to "Download", rails_blob_path(@article.cover, disposition: "attachment") %>
```

Validate uploads yourself:

```ruby
validate :acceptable_cover

def acceptable_cover
  return unless cover.attached?
  errors.add(:cover, "must be a JPEG or PNG") unless cover.content_type.in?(%w[image/jpeg image/png])
  errors.add(:cover, "is too big (max 5 MB)") if cover.byte_size > 5.megabytes
end
```

---

## 14.2 Variants and storage services

**Variants** are resized or transformed versions of an image, generated on demand and cached. They need the `image_processing` gem and the `libvips` library (or ImageMagick):

```ruby
# Gemfile
gem "image_processing", "~> 1.2"
```

```erb
<%= image_tag @article.cover.variant(resize_to_limit: [300, 300]) %>
```

Or declare named variants in the model:

```ruby
has_one_attached :cover do |attachable|
  attachable.variant :thumb, resize_to_limit: [100, 100]
end
```

```erb
<%= image_tag @article.cover.variant(:thumb) %>
```

**Storage services** are configured in `config/storage.yml`:

```yaml
local:
  service: Disk
  root: <%= Rails.root.join("storage") %>

amazon:
  service: S3
  access_key_id: <%= Rails.application.credentials.dig(:aws, :access_key_id) %>
  secret_access_key: <%= Rails.application.credentials.dig(:aws, :secret_access_key) %>
  region: us-east-1
  bucket: my-app-uploads
```

Choose one per environment:

```ruby
# config/environments/development.rb
config.active_storage.service = :local

# config/environments/production.rb
config.active_storage.service = :amazon
```

The S3 service needs `gem "aws-sdk-s3", require: false`. Google Cloud Storage and Azure are also supported.

For large files, use **direct uploads** (`form.file_field :cover, direct_upload: true`) so the browser sends the file straight to the storage service instead of through your server.

---

## 14.3 Sending email

**Action Mailer** works like controllers and views, but the result is an email.

Generate a mailer:

```bash
bin/rails generate mailer UserMailer welcome
```

```ruby
# app/mailers/user_mailer.rb
class UserMailer < ApplicationMailer
  default from: "hello@example.com"

  def welcome(user)
    @user = user
    mail(to: @user.email_address, subject: "Welcome to My App")
  end
end
```

Create the templates, one HTML and one plain text:

```erb
<%# app/views/user_mailer/welcome.html.erb %>
<h1>Welcome, <%= @user.name %>!</h1>
<p><%= link_to "Open the app", root_url %></p>
```

```erb
<%# app/views/user_mailer/welcome.text.erb %>
Welcome, <%= @user.name %>!
Open the app: <%= root_url %>
```

Send it:

```ruby
UserMailer.welcome(user).deliver_now     # sends immediately
UserMailer.welcome(user).deliver_later   # queues a background job (preferred)
```

Mailers need a host for URLs and a delivery method:

```ruby
# config/environments/production.rb
config.action_mailer.default_url_options = { host: "example.com", protocol: "https" }
config.action_mailer.delivery_method = :smtp
config.action_mailer.smtp_settings = {
  address: "smtp.example.com",
  port: 587,
  user_name: Rails.application.credentials.dig(:smtp, :user_name),
  password: Rails.application.credentials.dig(:smtp, :password),
  authentication: :plain,
  enable_starttls_auto: true
}
```

Use mailers in `_url` form (`root_url`), because emails need full addresses. Attachments: `attachments["report.pdf"] = File.read("report.pdf")` before calling `mail`.

---

## 14.4 Previews and testing mail

**Previews** show how an email looks without sending it. Create a preview class:

```ruby
# test/mailers/previews/user_mailer_preview.rb
class UserMailerPreview < ActionMailer::Preview
  def welcome
    UserMailer.welcome(User.first)
  end
end
```

Visit `http://localhost:3000/rails/mailers` in development to browse them.

**Test delivery.** In the test environment emails are collected in memory (`config.action_mailer.delivery_method = :test`) rather than sent.

```ruby
# test/mailers/user_mailer_test.rb
require "test_helper"

class UserMailerTest < ActionMailer::TestCase
  test "welcome" do
    user = users(:one)
    email = UserMailer.welcome(user)

    assert_emails 1 do
      email.deliver_now
    end

    assert_equal ["hello@example.com"], email.from
    assert_equal [user.email_address], email.to
    assert_equal "Welcome to My App", email.subject
    assert_match "Welcome", email.body.encoded
  end
end
```

Testing that something enqueued an email from a controller or model:

```ruby
assert_enqueued_emails 1 do
  post users_url, params: { user: { ... } }
end
```

In development you can also use a tool like `letter_opener` (opens mail in the browser) or MailCatcher (a local SMTP inbox).

> Official references: [Active Storage Overview](https://guides.rubyonrails.org/active_storage_overview.html), [Action Mailer Basics](https://guides.rubyonrails.org/action_mailer_basics.html)

---

[Previous](./[13]-Authorization.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[15]-Background-Jobs-With-Active-Job.md)