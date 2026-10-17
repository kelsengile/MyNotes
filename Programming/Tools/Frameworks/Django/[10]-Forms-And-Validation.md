[Previous](./[9]-The-Django-Admin.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[11]-Class-Based-Views.md)

*Building Features*

# Lesson 10 - Forms And Validation

---

## 10.1 Form classes

Handling HTML forms by hand is repetitive and error-prone: you must render the fields, read the submitted data, validate it, redisplay errors, and stay safe against malicious input. Django's **forms** system does all of that. A form class describes the fields once, and Django uses that description to render HTML, validate input, and convert submitted text into Python values.

### Defining a form

Forms usually live in a `forms.py` file inside an app:

```python
# blog/forms.py
from django import forms


class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    subject = forms.ChoiceField(choices=[("general", "General"), ("bug", "Bug report")])
    message = forms.CharField(widget=forms.Textarea)
    subscribe = forms.BooleanField(required=False)
```

### Common form fields

| Field | Produces | Python value |
|---|---|---|
| `CharField` | Text input | `str` |
| `IntegerField`, `FloatField`, `DecimalField` | Number input | `int`, `float`, `Decimal` |
| `BooleanField` | Checkbox | `bool` (use `required=False` for optional checkboxes) |
| `EmailField`, `URLField` | Text input with validation | `str` |
| `DateField`, `DateTimeField` | Date input | `date`, `datetime` |
| `ChoiceField` | Select | `str` |
| `MultipleChoiceField` | Multi-select | `list` |
| `ModelChoiceField` | Select of model objects | model instance |
| `FileField`, `ImageField` | File input | `UploadedFile` |

### Common field options

| Option | Meaning |
|---|---|
| `required=False` | The field may be left empty (fields are required by default) |
| `label="..."` | The label text |
| `initial=...` | Pre-filled value |
| `help_text="..."` | Hint shown near the field |
| `widget=...` | The HTML control used to render the field |
| `validators=[...]` | Extra validation functions |
| `disabled=True` | Shown but not editable; submitted value is ignored |

### Widgets

A **widget** controls the HTML that is produced, and is separate from validation. Pass HTML attributes with `attrs`:

```python
message = forms.CharField(
    widget=forms.Textarea(attrs={"rows": 4, "class": "input", "placeholder": "Your message"})
)
birthday = forms.DateField(widget=forms.DateInput(attrs={"type": "date"}))
```

### Using a form in a view

Forms follow a standard pattern: show an empty form on `GET`, validate on `POST`, and **redirect after success** so a page refresh does not resubmit the data:

```python
# blog/views.py
from django.shortcuts import redirect, render
from .forms import ContactForm


def contact(request):
    if request.method == "POST":
        form = ContactForm(request.POST)        # bound form
        if form.is_valid():
            name = form.cleaned_data["name"]
            email = form.cleaned_data["email"]
            # send an email, save something, etc.
            return redirect("blog:contact_thanks")
    else:
        form = ContactForm()                    # unbound form

    return render(request, "blog/contact.html", {"form": form})
```

Key ideas:

- An **unbound** form has no data and is used for the first display.
- A **bound** form is created with submitted data (`request.POST`) and can be validated.
- `form.is_valid()` runs all validation. If it returns `True`, the cleaned and converted values are in `form.cleaned_data`.
- If the form is invalid, render the **same bound form** again so the user sees their input and the errors.

### Rendering the form in a template

The quickest way is to output the whole form. Always include `{% csrf_token %}` in POST forms (Lesson 4, Lesson 17):

```html
<form method="post">
  {% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Send</button>
</form>
```

Other built-in layouts are `{{ form.as_table }}` and `{{ form.as_ul }}` (you supply the surrounding `<table>` or `<ul>`). Current Django versions render forms with `<div>` wrappers when you simply write `{{ form }}`.

For full control, render each field yourself:

```html
<form method="post">
  {% csrf_token %}

  {% for field in form %}
    <div class="field">
      {{ field.label_tag }}
      {{ field }}
      {% if field.help_text %}<small>{{ field.help_text }}</small>{% endif %}
      {% for error in field.errors %}
        <p class="error">{{ error }}</p>
      {% endfor %}
    </div>
  {% endfor %}

  <button type="submit">Send</button>
</form>
```

Useful per-field attributes: `field.label_tag`, `field.value`, `field.errors`, `field.help_text`, `field.id_for_label`, and `field.name`.

### GET forms

For search forms, use `method="get"` and no CSRF token, and bind with `request.GET`:

```python
form = SearchForm(request.GET or None)
if form.is_valid():
    results = Post.objects.filter(title__icontains=form.cleaned_data["q"])
```

### Initial values and the messages framework

```python
form = ContactForm(initial={"subject": "general"})
```

To show a one-time success message after a redirect, use the messages framework (enabled in default settings):

```python
from django.contrib import messages

messages.success(request, "Thanks, your message was sent.")
```

```html
{% if messages %}
  {% for message in messages %}
    <p class="{{ message.tags }}">{{ message }}</p>
  {% endfor %}
{% endif %}
```

---

## 10.2 ModelForms

Many forms exist only to create or edit a model instance. Writing the same fields twice (once in the model, once in the form) violates DRY. A **ModelForm** builds its fields directly from a model.

### Defining a ModelForm

```python
# blog/forms.py
from django import forms
from .models import Post


class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ["title", "slug", "body", "status"]
```

Django maps each model field to a suitable form field automatically (`CharField` to a text input, `TextField` to a textarea, `ForeignKey` to a select, and so on). `blank`, `max_length`, `choices`, and validators carry over as well.

### Choosing fields

`fields` is a whitelist and is **required** (or use `exclude`):

```python
class Meta:
    model = Post
    fields = ["title", "body"]              # recommended: list exactly what users may edit

    # exclude = ["author", "created_at"]    # allowed, but easy to expose new fields by accident
    # fields = "__all__"                    # avoid: every future field becomes editable
```

Listing fields explicitly prevents **mass assignment** problems, where a user could submit values for fields you never meant them to change (such as `is_staff` or `author`).

### Customizing labels, widgets, and help text

```python
class Meta:
    model = Post
    fields = ["title", "body", "status"]
    labels = {"title": "Post title"}
    help_texts = {"body": "Markdown is supported."}
    widgets = {
        "body": forms.Textarea(attrs={"rows": 10}),
    }
    error_messages = {
        "title": {"max_length": "That title is too long."},
    }
```

### Creating an object

```python
def post_create(request):
    if request.method == "POST":
        form = PostForm(request.POST)
        if form.is_valid():
            post = form.save()                  # INSERT into the database
            return redirect(post)               # uses get_absolute_url()
    else:
        form = PostForm()
    return render(request, "blog/post_form.html", {"form": form})
```

### Setting extra values before saving: commit=False

If some fields are not part of the form (such as the current user), save without committing, set them, then save for real:

```python
if form.is_valid():
    post = form.save(commit=False)      # builds the object but does not save yet
    post.author = request.user
    post.save()
    form.save_m2m()                     # needed for many-to-many fields when commit=False
```

If the form has no many-to-many fields, `save_m2m()` is not needed.

### Editing an object

Pass the existing object with `instance=`. The form is pre-filled, and `save()` runs an `UPDATE`:

```python
from django.shortcuts import get_object_or_404


def post_edit(request, pk):
    post = get_object_or_404(Post, pk=pk)
    if request.method == "POST":
        form = PostForm(request.POST, instance=post)
        if form.is_valid():
            form.save()
            return redirect(post)
    else:
        form = PostForm(instance=post)
    return render(request, "blog/post_form.html", {"form": form})
```

### Validation in ModelForms

A ModelForm validates the form fields **and** then runs the model's own validation (`full_clean()`, Lesson 5), including `unique=True` checks. Model-level rules therefore apply automatically.

### Form sets

To edit several objects at once, Django offers **formsets** and **inline formsets** (`modelformset_factory`, `inlineformset_factory`). They are an advanced topic, but follow the same bound/unbound pattern.

---

## 10.3 Validation and error display

### The validation process

When you call `is_valid()` (or access `form.errors`), Django runs these steps for each field in order:

1. **`to_python()`** converts the raw string to a Python type (for example text to `int`).
2. **`validate()`** checks built-in rules such as `required`.
3. **`run_validators()`** runs the field's validators (such as `max_length`).
4. **`clean_<fieldname>()`** runs your custom method for that field, if you wrote one.

After every field has been cleaned, the form's **`clean()`** method runs for rules that involve several fields.

### Field-level validation: clean_<fieldname>()

Define a method named `clean_` plus the field name. It must **return** the cleaned value or raise `ValidationError`:

```python
from django import forms
from django.core.exceptions import ValidationError


class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ["title", "slug", "body"]

    def clean_title(self):
        title = self.cleaned_data["title"]
        if "spam" in title.lower():
            raise ValidationError("Titles may not contain the word 'spam'.")
        return title.strip()
```

### Form-level validation: clean()

Use `clean()` when fields depend on each other:

```python
class SignupForm(forms.Form):
    password = forms.CharField(widget=forms.PasswordInput)
    password_confirm = forms.CharField(widget=forms.PasswordInput)

    def clean(self):
        cleaned = super().clean()
        if cleaned.get("password") != cleaned.get("password_confirm"):
            self.add_error("password_confirm", "Passwords do not match.")
        return cleaned
```

- Always call `super().clean()` first.
- Use `cleaned.get(...)` rather than square brackets, because a field that already failed will be missing from `cleaned_data`.
- `self.add_error("field_name", "message")` attaches the error to a field. Use `self.add_error(None, "message")` (or just raise `ValidationError` from `clean()`) for a **non-field error**.

### Reusable validators

A **validator** is a function that raises `ValidationError` when a value is invalid. It can be shared between forms and model fields:

```python
from django.core.exceptions import ValidationError


def validate_even(value):
    if value % 2 != 0:
        raise ValidationError("%(value)s is not an even number.", params={"value": value})


class Product(models.Model):
    quantity = models.IntegerField(validators=[validate_even])
```

Django ships with many validators in `django.core.validators`, such as `MinValueValidator`, `MaxValueValidator`, `RegexValidator`, and `EmailValidator`.

### Error messages

```python
title = forms.CharField(
    max_length=100,
    error_messages={
        "required": "Please enter a title.",
        "max_length": "That title is too long.",
    },
)
```

### Displaying errors

When a bound form is invalid:

- `form.errors` is a dictionary-like object of all errors.
- `form["field"].errors` lists the errors for one field.
- `form.non_field_errors` lists errors not tied to a specific field.

```html
{% if form.non_field_errors %}
  <div class="alert">{{ form.non_field_errors }}</div>
{% endif %}

{% for field in form %}
  <div>
    {{ field.label_tag }} {{ field }}
    {% for error in field.errors %}
      <span class="error">{{ error }}</span>
    {% endfor %}
  </div>
{% endfor %}
```

When you use `{{ form.as_p }}` or `{{ form }}`, errors are already rendered for you.

### Marking invalid fields

Add a CSS class to invalid inputs for styling:

```python
def __init__(self, *args, **kwargs):
    super().__init__(*args, **kwargs)
    for name, field in self.fields.items():
        field.widget.attrs.setdefault("class", "input")

# In a template, check field.errors to add an "invalid" class.
```

### Server-side validation is mandatory

HTML attributes like `required` or `maxlength` help users but are trivial to bypass. **Always** rely on Django's validation, and never trust data until `is_valid()` returns `True`. Read from `cleaned_data`, **not** from `request.POST`, after validation.

---

## 10.4 File uploads

### Prerequisites

Uploading files needs three things:

1. The form tag must use `enctype="multipart/form-data"`.
2. The view must pass `request.FILES` to the form.
3. A destination must be configured with `MEDIA_ROOT` (Lesson 14).

### Settings

```python
# settings.py
MEDIA_URL = "media/"
MEDIA_ROOT = BASE_DIR / "media"
```

### A model with a file

```python
class Document(models.Model):
    title = models.CharField(max_length=100)
    file = models.FileField(upload_to="documents/%Y/%m/")
    cover = models.ImageField(upload_to="covers/", blank=True)    # requires: pip install Pillow
```

`upload_to` is a folder under `MEDIA_ROOT`. It may contain `strftime` codes like `%Y/%m/`, or be a function for full control.

### The form

```python
class DocumentForm(forms.ModelForm):
    class Meta:
        model = Document
        fields = ["title", "file", "cover"]
```

### The view

```python
def upload(request):
    if request.method == "POST":
        form = DocumentForm(request.POST, request.FILES)    # note request.FILES
        if form.is_valid():
            form.save()
            return redirect("blog:document_list")
    else:
        form = DocumentForm()
    return render(request, "blog/upload.html", {"form": form})
```

### The template

```html
<form method="post" enctype="multipart/form-data">
  {% csrf_token %}
  {{ form.as_p }}
  <button type="submit">Upload</button>
</form>
```

Forgetting `enctype` is the most common cause of "the file field says it is required" errors, because the browser then sends only the file name.

### Plain (non-model) forms

```python
class UploadForm(forms.Form):
    file = forms.FileField()

def handle(request):
    form = UploadForm(request.POST, request.FILES)
    if form.is_valid():
        uploaded = form.cleaned_data["file"]
        print(uploaded.name, uploaded.size, uploaded.content_type)
        with open(f"/tmp/{uploaded.name}", "wb") as destination:
            for chunk in uploaded.chunks():
                destination.write(chunk)
```

Always use `.chunks()` for large files instead of `.read()`, so the whole file is not loaded into memory.

### Validating uploads

Never trust a file name or content type supplied by the browser. Add checks for size and extension:

```python
from django.core.exceptions import ValidationError
from django.core.validators import FileExtensionValidator


def validate_file_size(file):
    limit_mb = 5
    if file.size > limit_mb * 1024 * 1024:
        raise ValidationError(f"The file is too large. Maximum size is {limit_mb} MB.")


class Document(models.Model):
    file = models.FileField(
        upload_to="documents/",
        validators=[FileExtensionValidator(["pdf", "docx"]), validate_file_size],
    )
```

`ImageField` verifies that the upload really is an image using Pillow.

### Using the file

```python
doc.file.url      # "/media/documents/2026/03/report.pdf"
doc.file.name     # "documents/2026/03/report.pdf"
doc.file.size     # size in bytes
doc.file.path     # full path on disk (local storage only)
```

```html
<a href="{{ doc.file.url }}">Download</a>
<img src="{{ doc.cover.url }}" alt="{{ doc.title }}">
```

### Upload security checklist

- Limit the **size** and **type** of files accepted, and set `DATA_UPLOAD_MAX_MEMORY_SIZE` and `FILE_UPLOAD_MAX_MEMORY_SIZE` if needed.
- Do not execute or serve uploaded files from a location where the web server might run them as code.
- Django renames files on collision, but treat all uploaded names as untrusted.
- Serve user-uploaded content from a separate domain or storage service in production to reduce XSS risk (Lesson 14, Lesson 17).
- Only authenticated users should upload, unless there is a clear reason otherwise.

---

[Previous](./[9]-The-Django-Admin.md) | [Table of Contents](./[0]-Introduction-to-Django.md) | [Next](./[11]-Class-Based-Views.md)
