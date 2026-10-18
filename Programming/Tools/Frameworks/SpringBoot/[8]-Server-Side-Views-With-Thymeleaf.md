[Previous](./[7]-Validation-And-Exception-Handling.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[9]-Spring-Data-JPA.md)

*Building The Web Layer*

# Lesson 8 - Server-Side Views With Thymeleaf

Not every app is a JSON API. **Server-side rendering** builds HTML pages on the server and sends finished pages to the browser. Thymeleaf is Spring's most popular template engine.

---

## 8.1 Template basics

### Setup

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

Templates live in `src/main/resources/templates/` and end with `.html`.

### A controller returning a view

```java
@Controller                       // NOT @RestController: return values are view names
public class PageController {

    @GetMapping("/hello")
    public String hello(Model model) {
        model.addAttribute("name", "Ana");
        model.addAttribute("items", List.of("Apples", "Bread", "Milk"));
        model.addAttribute("admin", true);
        return "hello";           // renders templates/hello.html
    }
}
```

### The template (`templates/hello.html`)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <title>Hello</title>
</head>
<body>
  <h1 th:text="'Hello, ' + ${name} + '!'">Placeholder</h1>

  <ul>
    <li th:each="item : ${items}" th:text="${item}">Item</li>
  </ul>

  <p th:if="${admin}">You are an admin.</p>
  <p th:unless="${admin}">Standard user.</p>

  <a th:href="@{/hello(lang='en')}">English</a>
</body>
</html>
```

### Core syntax

| Expression | Meaning |
|---|---|
| `${...}` | A model variable |
| `@{...}` | A URL (adds the context path automatically) |
| `*{...}` | A property of the selected object (`th:object`) |
| `#{...}` | A message from a properties file (i18n) |
| `th:text` | Replace element text (HTML-escaped, so it is safe from XSS) |
| `th:utext` | Unescaped text, **dangerous with user input** |
| `th:each` | Loop |
| `th:if` / `th:unless` | Conditionals |

The template opens correctly in a browser even without the server, because the placeholder text sits in the tag body and is replaced at render time. This is called "natural templating".

### Static files

Place CSS, JS, and images in `src/main/resources/static/`. Reference them with `th:href="@{/css/site.css}"`.

---

## 8.2 Forms and binding

### The flow (Post/Redirect/Get)

1. `GET /contacts/new` shows an empty form.
2. `POST /contacts` validates and saves.
3. On success, **redirect** to another page. This prevents duplicate submission when the user refreshes.

### The form object

```java
public class ContactForm {

    @NotBlank(message = "Name is required")
    private String name;

    @NotBlank @Email
    private String email;

    // getters and setters (required for form binding)
}
```

### The controller

```java
@Controller
@RequestMapping("/contacts")
public class ContactController {

    @GetMapping("/new")
    public String showForm(Model model) {
        model.addAttribute("contactForm", new ContactForm());
        return "contact-form";
    }

    @PostMapping
    public String submit(@Valid @ModelAttribute("contactForm") ContactForm form,
                         BindingResult result,                 // must come right after the form
                         RedirectAttributes redirect) {
        if (result.hasErrors()) {
            return "contact-form";                             // redisplay with errors
        }
        // save the contact here
        redirect.addFlashAttribute("message", "Contact saved!");
        return "redirect:/contacts/thanks";
    }

    @GetMapping("/thanks")
    public String thanks() { return "thanks"; }
}
```

### The template (`templates/contact-form.html`)

```html
<form th:action="@{/contacts}" th:object="${contactForm}" method="post">

  <label>Name</label>
  <input type="text" th:field="*{name}">
  <span th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Name error</span>

  <label>Email</label>
  <input type="email" th:field="*{email}">
  <span th:if="${#fields.hasErrors('email')}" th:errors="*{email}">Email error</span>

  <button type="submit">Save</button>
</form>
```

### What `th:field` does

- Generates `id`, `name`, and `value` attributes.
- Re-fills the field with the submitted value when validation fails.
- Works with `th:errors` to show validation messages.

### CSRF

With Spring Security present, Thymeleaf automatically adds a hidden CSRF token to forms that use `th:action`. Always use `th:action` rather than a plain `action`.

---

## 8.3 Fragments and layouts

Most pages share a header, navigation, and footer. **Fragments** let you define them once and reuse them.

### Defining fragments (`templates/fragments/layout.html`)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>

  <header th:fragment="header(title)">
    <h1 th:text="${title}">Title</h1>
    <nav>
      <a th:href="@{/}">Home</a> |
      <a th:href="@{/contacts/new}">New contact</a>
    </nav>
  </header>

  <footer th:fragment="footer">
    <p>&copy; 2026 My App</p>
  </footer>

</body>
</html>
```

### Using them

```html
<body>
  <div th:replace="~{fragments/layout :: header('Contacts')}"></div>

  <main>
    <p>Page content here</p>
  </main>

  <div th:replace="~{fragments/layout :: footer}"></div>
</body>
```

| Attribute | Behavior |
|---|---|
| `th:insert` | Inserts the fragment **inside** the host tag |
| `th:replace` | **Replaces** the host tag with the fragment |

### A full page layout (`templates/fragments/base.html`)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" th:fragment="page(title, content)">
<head>
  <meta charset="UTF-8">
  <title th:replace="${title}">Default title</title>
</head>
<body>
  <header>...</header>
  <main th:replace="${content}">Content goes here</main>
  <footer>...</footer>
</body>
</html>
```

Pages then pass in their own title and content through `th:replace="~{fragments/base :: page(~{::title}, ~{::main})}"`.

### Useful helpers

```html
<span th:text="${#temporals.format(created, 'dd MMM yyyy')}">date</span>
<span th:text="${#numbers.formatDecimal(price, 1, 2)}">0.00</span>
<span th:text="${#strings.capitalize(name)}">name</span>
```

Date formatting through `#temporals` is built into the Thymeleaf Spring Boot integration for `java.time` types.

---

## 8.4 When to choose views over APIs

### Server-rendered views

Best when:
- The app is mostly pages, forms, and dashboards (admin panels, internal tools, content sites).
- You want **one codebase** and one deployment, with no separate front-end build.
- SEO and fast first paint matter.
- The team knows Java better than JavaScript frameworks.
- You want security to be simple (session cookies and CSRF handled by Spring).

### REST API + separate front end

Best when:
- You need rich, highly interactive client behavior (React, Vue, Angular).
- Multiple clients consume the same back end (web, mobile, partners).
- Front-end and back-end teams work and deploy independently.

### Comparison

| Factor | Thymeleaf views | JSON API + SPA |
|---|---|---|
| Rendering | Server | Browser |
| Projects to maintain | 1 | 2 or more |
| Mobile app reuse | Poor | Excellent |
| Interactivity | Limited without extra JS | High |
| Learning curve | Low | Higher |

### A middle path

You can enhance server-rendered pages with small amounts of JavaScript. Libraries such as **htmx** let you update parts of a page by fetching HTML fragments from Thymeleaf endpoints, avoiding a full SPA.

### Mixing both

A single Boot app can have both `@Controller` classes (HTML) and `@RestController` classes (JSON). A common pattern is `/` for pages and `/api/**` for data.

[Previous](./[7]-Validation-And-Exception-Handling.md) | [Table of Contents](./[0]-Introduction-to-SpringBoot.md) | [Next](./[9]-Spring-Data-JPA.md)