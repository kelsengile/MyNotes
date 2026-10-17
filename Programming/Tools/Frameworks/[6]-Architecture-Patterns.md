[Previous](./[5]-Dependencies-And-Package-Management.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[7]-Development-Workflow.md)

*Application Structure*

# Lesson 6 - Architecture Patterns

As an application grows, the biggest challenge stops being "how do I write this feature?" and becomes "where should this code live?" An **architecture pattern** is a proven answer to that question: a way of dividing an application into parts with clear responsibilities. Most frameworks are built around one or more of these patterns, so recognizing them makes every framework easier to learn.

The unifying idea behind all of them is **separation of concerns**: each part of the code should have one job, so you can understand, change, and test it without disturbing the rest.

## 6.1 MVC (Model-View-Controller)

**MVC** splits an application into three roles:

| Part | Responsibility | Knows about |
|---|---|---|
| **Model** | The data and the rules about it (for example, "a task needs a title") | Data only, not how it is displayed |
| **View** | What the user sees; presentation only | How to display data |
| **Controller** | Receives user input, asks the model for or changes data, and chooses the view | Both model and view |

### The flow of a request

```text
   User
    │  1. clicks a link / submits a form
    ▼
┌────────────┐  2. asks for / changes data  ┌─────────┐
│ Controller │ ───────────────────────────► │  Model  │
│            │ ◄─────────────────────────── │ (data)  │
└────────────┘  3. returns data             └─────────┘
    │  4. passes data to a view
    ▼
┌────────────┐
│    View    │  5. renders HTML (or UI) shown to the user
└────────────┘
```

### A tiny MVC example

The following program has all three roles in a few lines:

```python
# Model: data and rules
class TaskModel:
    def __init__(self):
        self.tasks = [{"title": "Learn MVC", "done": False}]

    def add(self, title):
        self.tasks.append({"title": title, "done": False})


# View: presentation only
def render_tasks(tasks):
    lines = [f"[{'x' if t['done'] else ' '}] {t['title']}" for t in tasks]
    return "\n".join(lines)


# Controller: receives input, coordinates the model and the view
class TaskController:
    def __init__(self, model):
        self.model = model

    def create(self, title):
        self.model.add(title)
        return render_tasks(self.model.tasks)


controller = TaskController(TaskModel())
print(controller.create("Read about layers"))
```

Output:

```text
[ ] Learn MVC
[ ] Read about layers
```

The model knows nothing about how tasks are printed, and the view knows nothing about how tasks are stored. If you later wanted to display tasks as HTML instead of text, you would change only the view.

### Where you will meet MVC

- **Ruby on Rails, Laravel, Spring MVC, and ASP.NET Core MVC** are built around it directly.
- **Django** uses a close variation called **MTV** (Model-Template-View). Django's "template" plays the role of the view, and Django's "view" plays the role of the controller.
- **Backend-only APIs** often drop the view, since they return data (JSON) instead of pages. The result is sometimes described as "model and controller."
- **MVVM (Model-View-ViewModel)** is a relative of MVC used in some frontend and mobile frameworks. A *view model* prepares data for the view and keeps it in sync automatically.

---

## 6.2 Component-Based Architecture

Frontend frameworks (and many mobile frameworks) organize applications differently. Rather than splitting the app by *technical role* (model, view, controller), they split the **user interface into small, self-contained components**, each combining its own structure, behavior, and styling.

A web page can be thought of as a **tree of components**:

```text
App
├── Header
│   ├── Logo
│   └── NavigationMenu
├── TaskList
│   ├── TaskItem
│   ├── TaskItem
│   └── TaskItem
└── Footer
```

Each component:

- Does one job (a button, a form, a list, a whole page).
- Receives data from its parent, usually called **props** or **inputs**.
- Can send events back up to its parent, such as "the user clicked me."
- Can be **reused** in many places and tested alone.

Here is the idea written in React-style syntax. `TaskItem` is defined once and reused for every task:

```jsx
function TaskItem({ title, done }) {
  return <li>{done ? "✔" : "○"} {title}</li>;
}

function TaskList({ tasks }) {
  return (
    <ul>
      {tasks.map((task) => (
        <TaskItem key={task.id} title={task.title} done={task.done} />
      ))}
    </ul>
  );
}
```

Component-based design is the foundation of React, Angular, Vue.js, Svelte, Flutter (where components are called *widgets*), and React Native. Lesson 8 covers components in depth.

> **MVC and components work together.** A full application often uses both: a component-based frontend that talks to an MVC- or layered backend over an API.

---

## 6.3 Layered and Modular Architecture

### Layered architecture

In a **layered architecture**, code is arranged in horizontal layers. Each layer has a specific role and is allowed to talk only to the layer directly beneath it.

```text
┌─────────────────────────────────────────┐
│ Presentation layer (controllers, routes)│  handles requests and responses
├─────────────────────────────────────────┤
│ Business layer (services)               │  the rules of the application
├─────────────────────────────────────────┤
│ Data access layer (repositories)        │  reads and writes the database
├─────────────────────────────────────────┤
│ Database                                │
└─────────────────────────────────────────┘
```

The rule is that dependencies point **downward**. The presentation layer calls the business layer, which calls the data layer, but never the other way around.

Here is the idea in code. Each layer has one responsibility, and you can see how the layers talk only to the one below:

```python
# Data access layer: talks to storage
class TaskRepository:
    def __init__(self):
        self._tasks = {}

    def save(self, task):
        self._tasks[task["id"]] = task

    def find(self, task_id):
        return self._tasks.get(task_id)


# Business layer: rules of the application
class TaskService:
    def __init__(self, repository):
        self.repository = repository

    def create_task(self, task_id, title):
        if not title.strip():
            raise ValueError("Title must not be empty")
        task = {"id": task_id, "title": title.strip(), "done": False}
        self.repository.save(task)
        return task


# Presentation layer: translates between the outside world and the service
def handle_create_request(service, body):
    try:
        task = service.create_task(body["id"], body["title"])
        return 201, task
    except ValueError as error:
        return 400, {"error": str(error)}


service = TaskService(TaskRepository())
print(handle_create_request(service, {"id": 1, "title": "Layered design"}))
print(handle_create_request(service, {"id": 2, "title": "   "}))
```

Output:

```text
(201, {'id': 1, 'title': 'Layered design', 'done': False})
(400, {'error': 'Title must not be empty'})
```

The benefit shows up when something changes. If you move from one database to another, only the repository changes. If the business rules change (say, titles must be at least three characters), only the service changes. The presentation layer does not care.

Spring Boot and ASP.NET Core applications are very commonly organized this way, with names such as controller, service, and repository.

### Modular architecture

A **modular architecture** groups code by *feature* instead of by technical layer. Instead of one big folder of all controllers and another of all models, you have a folder per feature that contains everything that feature needs.

| Organized by layer | Organized by feature (modular) |
|---|---|
| `controllers/` all controllers | `tasks/` controller, service, model together |
| `services/` all services | `users/` controller, service, model together |
| `models/` all models | `billing/` controller, service, model together |

Feature-based grouping scales better in large codebases, because working on "billing" means working in one place. Many frameworks have a built-in notion of modules:

- **Django** projects are made of *apps*.
- **NestJS** and **Angular** use *modules* (Angular also supports standalone components).
- **Laravel** and **Spring Boot** projects are commonly divided into feature packages.

### Monolith vs microservices

These terms describe how the whole system is *deployed*, not how a single codebase is organized:

- A **monolith** is a single application, deployed as one unit. It is simple to build, run, and debug, and is the best starting point for most projects.
- **Microservices** split a system into many small, independently deployed services that talk over the network. They can scale and be owned by separate teams, but add significant complexity.

A well-organized, modular monolith is often the best of both worlds when starting out.

---

## 6.4 Choosing a Pattern

You rarely choose a pattern from a blank slate. Your framework usually suggests one, and your job is to follow it and organize within it.

| Situation | Pattern that usually fits |
|---|---|
| Building a server-rendered website with Rails, Laravel, Django, or Spring MVC | **MVC** (or MTV) |
| Building the interface with React, Angular, Vue.js, Svelte, Flutter, or React Native | **Component-based** |
| Building an API with growing business rules | **Layered** (controller, service, repository) |
| A codebase with many features and many developers | **Modular**, grouped by feature |
| A full web application with a frontend and a backend | **Combination**: components on the frontend, layered or MVC on the backend |

A few practical guidelines:

1. **Follow your framework's conventions first.** Fighting the framework's intended structure usually creates pain.
2. **Start simple.** A small project does not need five layers. Add structure when the code starts to feel tangled, not before.
3. **Keep each part focused.** If a function validates input, applies business rules, and writes to the database all at once, it is a sign that the responsibilities should be separated.
4. **Keep dependencies pointing one way.** Mixing database code into page-rendering code makes both harder to change.
5. **Be consistent.** A mediocre pattern applied consistently beats a clever one applied unevenly.

Patterns are tools, not rules. They exist to keep your code understandable as it grows, and the right amount of structure is the amount that makes the next change easier.

---

[Previous](./[5]-Dependencies-And-Package-Management.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[7]-Development-Workflow.md)
