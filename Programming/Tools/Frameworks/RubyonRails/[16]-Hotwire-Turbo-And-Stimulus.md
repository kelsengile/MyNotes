[Previous](./[15]-Background-Jobs-With-Active-Job.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[17]-Building-APIs-With-Rails.md)

*Application Features*

# Lesson 16 - Hotwire: Turbo And Stimulus

**Hotwire** is Rails' approach to modern, fast interfaces without writing a large JavaScript app. The server sends **HTML** (not JSON), and small libraries update the page. It has two main parts: **Turbo** and **Stimulus**.

## 16.1 Turbo Drive

**Turbo Drive** is on by default. It intercepts link clicks and form submissions, fetches the result in the background, and swaps the page's `<body>`, so navigation feels instant without a full reload. Your page's `<head>` assets stay loaded.

What you need to know:

- **Forms must respond correctly.** After a failed submission, render with a **422** status so Turbo displays the form with errors. After success, `redirect_to`.

```ruby
if @article.save
  redirect_to @article
else
  render :new, status: :unprocessable_entity
end
```

- **Opt out** when needed:

```erb
<%= link_to "Download", file_path, data: { turbo: false } %>
<%= form_with model: @article, data: { turbo: false } do |f| %>
```

- **Non-GET links** use Turbo data attributes:

```erb
<%= link_to "Delete", @article, data: { turbo_method: :delete, turbo_confirm: "Sure?" } %>
<%= button_to "Delete", @article, method: :delete,
              form: { data: { turbo_confirm: "Sure?" } } %>
```

- Keep an element across navigations with `data-turbo-permanent` (it needs an `id`).
- Prefetch and a progress bar are built in. Pages are cached for the "back" button.
- JavaScript that runs on page load should listen for `turbo:load` (not just `DOMContentLoaded`), or be written as a Stimulus controller (16.4).

---

## 16.2 Turbo Frames

A **Turbo Frame** is a region of a page that can be updated on its own. Links and forms *inside* a frame only replace that frame.

```erb
<%# app/views/articles/show.html.erb %>
<%= turbo_frame_tag dom_id(@article, :title) do %>
  <h1><%= @article.title %></h1>
  <%= link_to "Edit title", edit_article_path(@article) %>
<% end %>
```

```erb
<%# app/views/articles/edit.html.erb %>
<%= turbo_frame_tag dom_id(@article, :title) do %>
  <%= form_with model: @article do |form| %>
    <%= form.text_field :title %>
    <%= form.submit "Save" %>
  <% end %>
<% end %>
```

Clicking "Edit title" loads `edit`, and Turbo extracts only the frame with the **matching id** and swaps it in. After saving (redirect to `show`), the frame goes back to the title view. No JavaScript written.

Useful options:

| Option | Effect |
|---|---|
| `target: "_top"` | Make a link/form inside a frame navigate the whole page |
| `src: path` | Load the frame's content from another URL |
| `loading: :lazy` | Load it when the frame scrolls into view |
| `data-turbo-action="advance"` | Update the URL when the frame navigates |

Lazy loading example:

```erb
<%= turbo_frame_tag "recommended", src: recommendations_path, loading: :lazy do %>
  <p>Loading...</p>
<% end %>
```

`dom_id(@article)` produces ids like `article_1`, which are handy for unique frame ids.

---

## 16.3 Turbo Streams

**Turbo Streams** send small instructions to change *specific parts* of a page: append, prepend, replace, update, remove, before, after.

Respond to a form with a stream:

```ruby
def create
  @comment = @article.comments.build(comment_params)
  respond_to do |format|
    if @comment.save
      format.turbo_stream
      format.html { redirect_to @article }
    else
      format.html { render :new, status: :unprocessable_entity }
    end
  end
end
```

```erb
<%# app/views/comments/create.turbo_stream.erb %>
<%= turbo_stream.append "comments", partial: "comments/comment", locals: { comment: @comment } %>
<%= turbo_stream.update "comment_form", partial: "comments/form", locals: { comment: Comment.new } %>
```

```erb
<%# the page %>
<div id="comments">
  <%= render @article.comments %>
</div>
```

**Live updates for everyone** use broadcasts over WebSockets (Action Cable, Lesson 21):

```erb
<%# subscribe on the page %>
<%= turbo_stream_from @article %>
```

```ruby
class Comment < ApplicationRecord
  belongs_to :article
  after_create_commit -> { broadcast_append_to article, target: "comments" }
end
```

Or the shortcut `broadcasts_to :article`. Now every user viewing that article sees new comments instantly.

Stream actions: `append`, `prepend`, `replace`, `update`, `remove`, `before`, `after`, and `refresh`. `replace` swaps the whole target element, and `update` replaces only its inside content.

---

## 16.4 Stimulus controllers

**Stimulus** adds behavior to HTML with small JavaScript controllers. You connect them with `data-` attributes.

```js
// app/javascript/controllers/hello_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["name", "output"]
  static values = { greeting: { type: String, default: "Hello" } }

  connect() {
    console.log("controller connected")
  }

  greet() {
    this.outputTarget.textContent = `${this.greetingValue}, ${this.nameTarget.value}!`
  }
}
```

```erb
<div data-controller="hello" data-hello-greeting-value="Hi">
  <input data-hello-target="name" type="text">
  <button data-action="click->hello#greet">Greet</button>
  <p data-hello-target="output"></p>
</div>
```

How the pieces fit:

| Attribute | Meaning |
|---|---|
| `data-controller="hello"` | Attach `hello_controller.js` to this element |
| `data-action="click->hello#greet"` | On click, call `greet()` |
| `data-hello-target="name"` | Makes `this.nameTarget` available |
| `data-hello-greeting-value="Hi"` | Provides `this.greetingValue` |

- File `hello_controller.js` maps to the identifier `hello`. A file named `user_profile_controller.js` is `user-profile`.
- `connect()` runs when the element appears in the DOM, and `disconnect()` when it leaves. This works well with Turbo's page swaps.
- Controllers in `app/javascript/controllers` are loaded automatically. With import maps, add third-party packages via `bin/importmap pin <package>`.

Use Stimulus for small interactions: toggles, dropdowns, clipboard buttons, form tweaks. Let Turbo handle fetching HTML.

> Official references: [Turbo Handbook](https://turbo.hotwired.dev/handbook/introduction), [Stimulus Handbook](https://stimulus.hotwired.dev/handbook/introduction)

---

[Previous](./[15]-Background-Jobs-With-Active-Job.md) | [Table of Contents](./[0]-Introduction-to-RubyonRails.md) | [Next](./[17]-Building-APIs-With-Rails.md)