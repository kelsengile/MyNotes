[Previous](./[5]-Controllers.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[7]-Forms-And-Form-Helpers.md)

*Views*

# Lesson 6 - Views And Templates

## 6.1 ERB basics

**ERB** (Embedded Ruby) templates mix HTML with Ruby. Files are named like `show.html.erb`.

| Tag | Meaning |
|---|---|
| `<% code %>` | Run Ruby, print nothing |
| `<%= code %>` | Run Ruby and print the result |
| `<%# comment %>` | Comment (not sent to the browser) |
| `<%- code -%>` / `<%= code -%>` | Trim surrounding whitespace |

```erb
<h1><%= @article.title %></h1>

<% if @article.published? %>
  <p>Published on <%= @article.created_at.to_date %></p>
<% else %>
  <p>Draft</p>
<% end %>

<ul>
  <% @articles.each do |article| %>
    <li><%= article.title %></li>
  <% end %>
</ul>
```

**Escaping:** `<%= %>` automatically HTML-escapes output, so `<script>` typed by a user is shown as text rather than executed. This protects you from XSS.

Only mark text as safe when *you* wrote it, and never for user input:

```erb
<%= raw "<b>trusted markup</b>" %>
<%= "<b>trusted</b>".html_safe %>
```

Keep logic out of templates: do queries in controllers or models, and formatting in helpers. A template should mostly display.

---

## 6.2 Layouts

A **layout** is the HTML wrapper shared by pages. The default is `app/views/layouts/application.html.erb`:

```erb
<!DOCTYPE html>
<html>
  <head>
    <title><%= content_for(:title) || "My Blog" %></title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    <%= stylesheet_link_tag "application" %>
    <%= javascript_importmap_tags %>
  </head>
  <body>
    <%= yield %>
  </body>
</html>
```

- `<%= yield %>` is where the current page's template is inserted.
- `csrf_meta_tags` supports CSRF protection for forms and requests.
- Your exact tags depend on your asset and JavaScript setup.

**`content_for`** lets a page send content to a named spot in the layout:

```erb
<%# in show.html.erb %>
<% content_for :title, @article.title %>
```

```erb
<%# in the layout %>
<title><%= yield(:title) || "My Blog" %></title>
```

**Choosing a different layout:**

```ruby
class Admin::BaseController < ApplicationController
  layout "admin"      # app/views/layouts/admin.html.erb
end
```

You can also pick one per render: `render layout: "minimal"`, or disable it with `layout: false`.

---

## 6.3 Partials

A **partial** is a reusable template fragment. Its filename starts with an underscore, but you reference it **without** the underscore.

```erb
<%# app/views/articles/_article.html.erb %>
<article>
  <h2><%= link_to article.title, article %></h2>
  <p><%= truncate(article.body, length: 120) %></p>
</article>
```

Render it:

```erb
<%= render partial: "articles/article", locals: { article: @article } %>
<%= render "articles/article", article: @article %>   <%# shorter %>
<%= render @article %>                                 <%# convention shortcut %>
```

Render a whole collection (Rails loops for you and uses `_article.html.erb`):

```erb
<%= render @articles %>
```

For an empty collection use `render(@articles) || "No articles yet."`.

**Locals** are the variables passed to a partial. Prefer locals over instance variables, so the partial is reusable.

Shared partials are often placed in `app/views/shared/` and rendered with `render "shared/header"`.

---

## 6.4 View helpers

**Helpers** are methods available in views that generate HTML or format data. Rails ships with many.

```erb
<%= link_to "Home", root_path, class: "nav-link" %>
<%= link_to "Delete", article_path(@article),
            data: { turbo_method: :delete, turbo_confirm: "Sure?" } %>
<%= button_to "Delete", @article, method: :delete %>

<%= image_tag "logo.png", alt: "Logo", width: 120 %>

<%= number_to_currency(1234.5) %>              <%# $1,234.50 %>
<%= number_with_delimiter(1234567) %>          <%# 1,234,567 %>
<%= truncate("A long sentence here", length: 10) %>
<%= pluralize(3, "comment") %>                 <%# 3 comments %>
<%= time_ago_in_words(@article.created_at) %> ago
<%= simple_format(@article.body) %>            <%# newlines → <p>/<br> %>

<%= tag.div "Hello", class: "box", id: "greeting" %>
```

`button_to` creates a small form, which is the most robust way to send DELETE/PATCH requests.

**Write your own helpers** in `app/helpers`. Methods in these modules are available in all views:

```ruby
# app/helpers/articles_helper.rb
module ArticlesHelper
  def status_badge(article)
    label = article.published? ? "Published" : "Draft"
    tag.span label, class: "badge #{label.downcase}"
  end
end
```

```erb
<%= status_badge(@article) %>
```

Use helpers to keep templates clean and avoid repeating small bits of presentation logic.

> Official references: [Layouts and Rendering](https://guides.rubyonrails.org/layouts_and_rendering.html), [Action View Helpers](https://guides.rubyonrails.org/action_view_helpers.html)

---

[Previous](./[5]-Controllers.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[7]-Forms-And-Form-Helpers.md)