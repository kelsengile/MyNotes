[Previous](./[14]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[16]-Performance-And-Change-Detection.md)

*Quality And Delivery*

# Lesson 15 - Testing Angular Applications

Tests let you change code with confidence. This lesson covers the test tools Angular projects use, how to test components and services, and how to test whole user flows in a real browser.

---

## 15.1 Jasmine, Karma, and Jest

A unit test needs two things: a **test framework** (describes tests and assertions) and a **test runner** (executes them, often in a browser).

| Tool | Role | Notes |
|---|---|---|
| **Jasmine** | Framework (`describe`, `it`, `expect`) | Long-time Angular default |
| **Karma** | Runner that launches a real browser | Deprecated; older projects still use it |
| **Jest** | Framework + runner in Node with a simulated DOM | Popular in the community |
| **Vitest** | Framework + runner, Jest-like API, very fast | Default for new projects in recent Angular versions |

Which one your project uses depends on its Angular version and setup. Check `package.json` and `angular.json`. The core API is almost identical across all of them:

```ts
describe('add()', () => {
  it('adds two numbers', () => {
    expect(1 + 2).toBe(3);
  });

  it('compares objects by value', () => {
    expect({ a: 1 }).toEqual({ a: 1 });
  });
});
```

Common matchers: `toBe`, `toEqual`, `toBeTruthy`, `toContain`, `toThrow`, `toHaveBeenCalled`.

Differences to be aware of are mostly in **mocks/spies**:

```ts
// Jasmine
const spy = jasmine.createSpy('save');
// Jest / Vitest (Vitest uses `vi.fn()`, Jest uses `jest.fn()`)
const spy = vi.fn();
```

Run tests with:

```bash
ng test                  # watch mode while developing
ng test --no-watch       # single run, for CI
```

Test files end in `.spec.ts` and sit next to the code they test. The CLI creates one for each `ng generate component` or `service`.

Structure each test in three steps: **Arrange** (set up), **Act** (do the thing), **Assert** (check the result).

---

## 15.2 TestBed and component tests

`TestBed` builds a small Angular environment for a component, using the same dependency injection as your app.

Component under test:

```ts
@Component({
  selector: 'app-greeting',
  template: `<h1>Hello, {{ name() }}!</h1><button (click)="clicked.emit()">Hi</button>`,
})
export class GreetingComponent {
  name = input.required<string>();
  clicked = output<void>();
}
```

Test:

```ts
import { ComponentFixture, TestBed } from '@angular/core/testing';

describe('GreetingComponent', () => {
  let fixture: ComponentFixture<GreetingComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [GreetingComponent],          // standalone components go in imports
    }).compileComponents();

    fixture = TestBed.createComponent(GreetingComponent);
    fixture.componentRef.setInput('name', 'Ada');   // set signal inputs this way
    fixture.detectChanges();                        // render
  });

  it('shows the name', () => {
    const h1 = fixture.nativeElement.querySelector('h1') as HTMLElement;
    expect(h1.textContent).toContain('Hello, Ada!');
  });

  it('emits when the button is clicked', () => {
    let emitted = false;
    fixture.componentInstance.clicked.subscribe(() => (emitted = true));

    (fixture.nativeElement.querySelector('button') as HTMLButtonElement).click();

    expect(emitted).toBe(true);
  });
});
```

Key points:

- `fixture.componentInstance` is the component class; `fixture.nativeElement` is its DOM.
- Call `fixture.detectChanges()` after changing state so the template updates. In zoneless apps, `await fixture.whenStable()` is also commonly used.
- Test **behavior the user sees** (text, events), not private internals.

**Faking dependencies** keeps tests isolated:

```ts
await TestBed.configureTestingModule({
  imports: [TaskListComponent],
  providers: [
    { provide: TaskApi, useValue: { list: () => of([{ id: 1, title: 'Test', done: false }]) } },
  ],
}).compileComponents();
```

Because DI resolves the fake, the component never touches the network. Shallow tests like this are quick and stable.

---

## 15.3 Testing services and HTTP

**A service with no dependencies** needs no `TestBed` at all:

```ts
it('adds a task', () => {
  const store = new CartStore();
  store.add({ id: 1, price: 10, qty: 2 });
  expect(store.total()).toBe(20);
});
```

If the service uses `inject()`, create it through `TestBed`:

```ts
beforeEach(() => TestBed.configureTestingModule({}));

it('is created', () => {
  const service = TestBed.inject(CartStore);
  expect(service).toBeTruthy();
});
```

**HTTP services** are tested without a real server, using `HttpTestingController`:

```ts
import { provideHttpClient } from '@angular/common/http';
import { HttpTestingController, provideHttpClientTesting } from '@angular/common/http/testing';

describe('TaskApi', () => {
  let api: TaskApi;
  let http: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [provideHttpClient(), provideHttpClientTesting()],
    });
    api = TestBed.inject(TaskApi);
    http = TestBed.inject(HttpTestingController);
  });

  afterEach(() => http.verify());          // fails if an unexpected request remained

  it('loads tasks', () => {
    let result: Task[] = [];
    api.list().subscribe((t) => (result = t));

    const req = http.expectOne('https://api.example.com/tasks');
    expect(req.request.method).toBe('GET');
    req.flush([{ id: 1, title: 'A', done: false }]);   // reply with fake data

    expect(result.length).toBe(1);
  });

  it('handles errors', () => {
    let status = 0;
    api.list().subscribe({ error: (e) => (status = e.status) });

    http.expectOne('https://api.example.com/tasks')
        .flush('fail', { status: 500, statusText: 'Server Error' });

    expect(status).toBe(500);
  });
});
```

`flush` sends a fake response. `verify()` in `afterEach` catches forgotten or unexpected requests.

Testing async code: prefer `await fixture.whenStable()` or returning promises from `async` tests. For timer-based code use the test framework's fake timers.

---

## 15.4 End-to-end testing

**End-to-end (e2e)** tests drive the real app in a real browser like a user: open a page, click, type, check what appears. They are slower and more fragile than unit tests, so write fewer of them and focus on critical journeys (log in, check out).

Tools:

| Tool | Notes |
|---|---|
| **Playwright** | Fast, multi-browser, great tooling; widely recommended |
| **Cypress** | Friendly interactive runner |
| **Protractor** | The former Angular default; **retired**, do not use for new projects |

The CLI can scaffold e2e support: run `ng e2e` and choose a tool when prompted. Example with Playwright:

```ts
// e2e/tasks.spec.ts
import { test, expect } from '@playwright/test';

test('user can add a task', async ({ page }) => {
  await page.goto('http://localhost:4200/tasks');

  await page.getByPlaceholder('New task').fill('Buy milk');
  await page.getByRole('button', { name: 'Add' }).click();

  await expect(page.getByText('Buy milk')).toBeVisible();
});
```

Tips for stable e2e tests:

- Select elements the way a user finds them: by **role, label, and visible text**, not by CSS classes.
- Never use fixed sleeps; use the tool's auto-waiting assertions (`toBeVisible`).
- Keep tests independent: each should set up its own data.
- Use a dedicated test backend or mock network responses.

The **testing pyramid** is a useful guide: many fast unit tests, a moderate number of component tests, and a few e2e tests covering the main flows.

---

**Further reading:** [Testing guide](https://angular.dev/guide/testing)

[Previous](./[14]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[16]-Performance-And-Change-Detection.md)
