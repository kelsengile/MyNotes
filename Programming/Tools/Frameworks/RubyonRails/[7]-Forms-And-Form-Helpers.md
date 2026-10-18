[Previous](./[6]-Views-And-Templates.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[8]-Active-Record-Models.md)

*Views*

# Lesson 7 - Forms And Form Helpers

## 7.1 form_with

`form_with` is the helper for building HTML forms. It also adds a hidden **authenticity token** to protect against CSRF.

**Without a model** (e.g. a search box). Provide a `url:`:

```erb
<%= form_with url: search_path, method: :get do |form| %>
  <%= form.label :q, "Search" %>
  <%= form.text_field :q, value: params[:q] %>
  <%= form.submit "Go" %>
<% end %>
```

This sends `GET /search?q=...`.

**With a scope** (fields grouped under a key):

```erb
<%= form_with scope: :contact, url: contacts_path do |form| %>
  <%= form.text_field :email %>   <%# params[:contact][:email] %>
<% end %>
```

Common field helpers:

| Helper | Output |
|---|---|
| `text_field` | `<input type="text">` |
| `text_area` | `<textarea>` |
| `email_field`, `password_field`, `number_field`, `date_field` | Typed inputs |
| `check_box` | Checkbox (also sends a hidden `0`) |
| `radio_button` | Radio option |
| `select` | Dropdown |
| `hidden_field` | Hidden input |
| `submit` | Submit button |

---

## 7.2 Model-backed forms

Passing a model makes Rails infer the URL, HTTP method, field values, and parameter names:

```erb
<%# app/views/articles/_form.html.erb %>
<%= form_with model: article do |form| %>
  <% if article.errors.any? %>
    <div class="errors">
      <h3><%= pluralize(article.errors.count, "error") %> prevented saving:</h3>
      <ul>
        <% article.errors.full_messages.each do |message| %>
          <li><%= message %></li>
        <% end %>
      </ul>
    </div>
  <% end %>

  <div>
    <%= form.label :title %>
    <%= form.text_field :title %>
  </div>

  <div>
    <%= form.label :body %>
    <%= form.text_area :body, rows: 8 %>
  </div>

  <div>
    <%= form.label :status %>
    <%= form.select :status, %w[draft published], include_blank: "Choose..." %>
  </div>

  <div>
    <%= form.check_box :featured %>
    <%= form.label :featured %>
  </div>

  <%= form.submit %>
<% end %>
```

**One form for new and edit.** If `article` is a new record, Rails builds `POST /articles`. If it's saved, Rails builds a `PATCH /articles/1` (using a hidden `_method` field). The submit button text switches between "Create Article" and "Update Article" automatically.

Use the partial from both templates:

```erb
<%# new.html.erb and edit.html.erb %>
<%= render "form", article: @article %>
```

For a dropdown filled from another model:

```erb
<%= form.collection_select :category_id, Category.all, :id, :name, prompt: true %>
```

Submitted data lands in `params[:article]`, which your controller permits with strong parameters (Lesson 5).

---

## 7.3 Flash messages

The **flash** is a small hash that survives for exactly *one* next request. It is perfect for confirmation messages after a redirect.

Set it in a controller:

```ruby
redirect_to @article, notice: "Article saved."
redirect_to root_path, alert: "You must sign in."
# equivalent long form:
flash[:notice] = "Article saved."
```

`notice` and `alert` have shortcuts on `redirect_to`. You can define your own types with `add_flash_types :success, :warning` in `ApplicationController`.

When you **render** (not redirect) and want a message for the *current* response, use `flash.now`:

```ruby
flash.now[:alert] = "Please fix the errors below."
render :new, status: :unprocessable_entity
```

Show flash messages in the layout:

```erb
<% flash.each do |type, message| %>
  <div class="flash flash-<%= type %>"><%= message %></div>
<% end %>
```

---

## 7.4 File fields

Use `file_field` to let users upload files. Rails automatically makes the form `multipart` when a file field is present.

```erb
<%= form_with model: @article do |form| %>
  <%= form.label :cover %>
  <%= form.file_field :cover, accept: "image/png,image/jpeg" %>
  <%= form.submit %>
<% end %>
```

Multiple files:

```erb
<%= form.file_field :photos, multiple: true %>
```

The model declares the attachments (covered in Lesson 14 with Active Storage):

```ruby
class Article < ApplicationRecord
  has_one_attached :cover
  has_many_attached :photos
end
```

Permit the field in the controller. For multiple files, permit an array:

```ruby
params.expect(article: [:title, :body, :cover, photos: []])
```

Show the upload:

```erb
<%= image_tag @article.cover if @article.cover.attached? %>
```

Always validate file type and size on the server. The `accept` attribute is only a browser hint and can be bypassed.

> Official reference: [Action View Form Helpers](https://guides.rubyonrails.org/form_helpers.html)

---

[Previous](./[6]-Views-And-Templates.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[8]-Active-Record-Models.md)