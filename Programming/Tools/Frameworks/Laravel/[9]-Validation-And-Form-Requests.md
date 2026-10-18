[Previous](./[8]-Seeding-And-Factories.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[10]-Authentication.md)

*Data*

# Lesson 9 - Validation And Form Requests

## 9.1 Validation rules

Never trust user input. Laravel validates it with a short rule syntax.

```php
public function store(Request $request)
{
    $validated = $request->validate([
        'title' => 'required|string|max:255',
        'body'  => ['required', 'string', 'min:10'],
        'email' => 'required|email|unique:users,email',
        'tags'  => 'array',
        'tags.*' => 'integer|exists:tags,id',
    ]);

    Post::create($validated);

    return redirect()->route('posts.index');
}
```

Rules can be a `|`-separated string or an array (arrays are clearer for complex rules).

If validation fails:

- For normal web requests, Laravel redirects back with the errors and the old input flashed to the session.
- For JSON/API requests, Laravel returns a `422` response with the errors in JSON.

**Commonly used rules:**

| Rule | Meaning |
|---|---|
| `required`, `nullable`, `sometimes` | Presence rules |
| `string`, `integer`, `numeric`, `boolean`, `array`, `date` | Type rules |
| `min:3`, `max:255`, `between:1,10`, `size:5` | Size (length, value, or file KB depending on type) |
| `email`, `url`, `uuid`, `ip` | Format rules |
| `unique:table,column` | Value must not already exist |
| `exists:table,column` | Value must exist |
| `confirmed` | Needs a matching `*_confirmation` field |
| `in:a,b,c` | Value must be one of these |
| `regex:/pattern/` | Matches a regular expression |
| `file`, `image`, `mimes:jpg,png`, `max:2048` | File rules |
| `bail` | Stop at the first failure for this field |

`$request->validated()` and the return value of `validate()` contain only the validated fields, which makes them safe to pass to `create()`.

---

## 9.2 Form request classes

For larger forms, move validation into its own class.

```bash
php artisan make:request StorePostRequest
```

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StorePostRequest extends FormRequest
{
    // Who may make this request?
    public function authorize(): bool
    {
        return true; // or e.g. $this->user()->can('create', Post::class)
    }

    public function rules(): array
    {
        return [
            'title' => ['required', 'string', 'max:255'],
            'body'  => ['required', 'string'],
        ];
    }

    public function messages(): array
    {
        return [
            'title.required' => 'Please give your post a title.',
        ];
    }

    public function attributes(): array
    {
        return ['body' => 'post content'];
    }

    // Optional: clean input before validating
    protected function prepareForValidation(): void
    {
        $this->merge(['title' => trim($this->title ?? '')]);
    }
}
```

Type-hint it in the controller. Validation runs before the method body:

```php
public function store(StorePostRequest $request)
{
    Post::create($request->validated());

    return redirect()->route('posts.index');
}
```

Benefits: slimmer controllers, reusable rules, and a dedicated spot for authorization.

For update requests, use `Rule::unique(...)->ignore($this->post)` so a record does not conflict with itself:

```php
use Illuminate\Validation\Rule;

'slug' => ['required', Rule::unique('posts', 'slug')->ignore($this->post)],
```

---

## 9.3 Displaying errors

Validation errors are available in every Blade view through the `$errors` variable.

```blade
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
```

Per-field errors with `@error` and previously entered values with `old()`:

```blade
<form method="POST" action="{{ route('posts.store') }}">
    @csrf

    <label for="title">Title</label>
    <input id="title" name="title" value="{{ old('title') }}">
    @error('title')
        <p class="error">{{ $message }}</p>
    @enderror

    <label for="body">Body</label>
    <textarea id="body" name="body">{{ old('body') }}</textarea>
    @error('body')
        <p class="error">{{ $message }}</p>
    @enderror

    <button type="submit">Save</button>
</form>
```

- `old('title')` repopulates the field after a failed submission. It does not flash passwords or files.
- To use named error groups (several forms on one page), call `$request->validateWithBag('login', [...])` and read `$errors->login`.
- For JSON requests the response looks like:

```json
{
  "message": "The title field is required.",
  "errors": { "title": ["The title field is required."] }
}
```

---

## 9.4 Custom rules

**Rule objects.** Generate one:

```bash
php artisan make:rule Uppercase
```

```php
<?php

namespace App\Rules;

use Closure;
use Illuminate\Contracts\Validation\ValidationRule;

class Uppercase implements ValidationRule
{
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        if (strtoupper($value) !== $value) {
            $fail('The :attribute must be uppercase.');
        }
    }
}
```

```php
use App\Rules\Uppercase;

$request->validate([
    'code' => ['required', new Uppercase],
]);
```

**Closure rules** for one-off checks:

```php
'title' => [
    'required',
    function (string $attribute, mixed $value, Closure $fail) {
        if ($value === 'forbidden') {
            $fail("The {$attribute} is invalid.");
        }
    },
],
```

**Conditional and built-in rule helpers:**

```php
use Illuminate\Validation\Rule;

'status' => ['required', Rule::in(['draft', 'live'])],
'role'   => [Rule::requiredIf($request->is_staff)],
'email'  => ['required', Rule::unique('users')->where('active', true)],
```

You can also validate manually with the `Validator` facade when you are outside an HTTP request:

```php
use Illuminate\Support\Facades\Validator;

$validator = Validator::make($data, ['email' => 'required|email']);
if ($validator->fails()) {
    // $validator->errors()
}
```

[Previous](./[8]-Seeding-And-Factories.md) | [Table of Contents](./[0]-Introduction-to-Laravel.md) | [Next](./[10]-Authentication.md)