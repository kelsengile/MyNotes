[Previous](./[3]-Components-And-Templates.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[5]-Component-Communication.md)

*Core Concepts*

# Lesson 4 - Data Binding And Directives

Data binding connects your component class to the template. This lesson covers every binding form, the directives that change how elements behave, and pipes that format values.

---

## 4.1 Interpolation and property binding

Binding from class to template (one direction):

| Syntax | Name | Use |
|---|---|---|
| `{{ value }}` | Interpolation | Insert text |
| `[property]="expr"` | Property binding | Set a DOM property |
| `[attr.name]="expr"` | Attribute binding | Set an HTML attribute with no matching property |
| `[class.name]="expr"` | Class binding | Toggle one CSS class |
| `[style.prop]="expr"` | Style binding | Set one CSS style |

```ts
@Component({
  selector: 'app-profile',
  template: `
    <img [src]="photoUrl" [alt]="name" />
    <h2>{{ name }}</h2>
    <button [disabled]="!canSave">Save</button>
    <p [class.error]="hasError" [style.font-size.px]="size">Status</p>
    <button [attr.aria-label]="'Close ' + name">X</button>
  `,
})
export class ProfileComponent {
  name = 'Ada';
  photoUrl = '/ada.png';
  canSave = false;
  hasError = true;
  size = 18;
}
```

Key idea: **square brackets bind to DOM properties, not HTML attributes.** The HTML attribute `value` only sets the *initial* value of an input, while the DOM property `value` is the *current* value. Angular binds the property. Use `[attr.x]` only for attributes such as `aria-*`, `colspan`, or `role` that have no property.

You can bind to a whole class list too:

```html
<div [class]="{ active: isActive, disabled: !enabled }">...</div>
```

Do not mix `{{ }}` and `[ ]` on the same attribute for the same thing; `src="{{ photoUrl }}"` and `[src]="photoUrl"` are equivalent, and square brackets are the more common style.

---

## 4.2 Event and two-way binding

**Event binding** flows from template to class, using parentheses:

```html
<button (click)="increment()">+1</button>
<input (input)="onType($event)" />
<input (keydown.enter)="submit()" />
```

```ts
onType(event: Event): void {
  const input = event.target as HTMLInputElement;
  this.text = input.value;
}
```

`$event` is the event object. Key filters such as `keydown.enter` or `keydown.escape` save you from checking `event.key` manually. You can also write short statements: `(click)="count = count + 1"`.

**Two-way binding** combines both directions with `[( )]`, nicknamed "banana in a box":

```ts
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-search',
  imports: [FormsModule],
  template: `
    <input [(ngModel)]="query" />
    <p>You typed: {{ query }}</p>
  `,
})
export class SearchComponent {
  query = '';
}
```

`[(ngModel)]="query"` is shorthand for `[ngModel]="query" (ngModelChange)="query = $event"`. `ngModel` comes from `FormsModule`, so you must import it. Child components can support the same syntax with `model()` (Lesson 6).

---

## 4.3 Attribute and structural directives

A **directive** adds behavior to an element. There are three kinds:

1. **Components** are directives with a template.
2. **Attribute directives** change an element's appearance or behavior.
3. **Structural directives** add or remove elements (the old `*ngIf` / `*ngFor`; now mostly replaced by `@if` / `@for`).

Built-in attribute directives (import them from `@angular/common`):

```ts
import { NgClass, NgStyle } from '@angular/common';

@Component({
  imports: [NgClass, NgStyle],
  template: `
    <p [ngClass]="{ big: isBig, red: isRed }">Text</p>
    <p [ngStyle]="{ 'font-size.px': size }">Text</p>
  `,
})
```

Plain `[class.x]` and `[style.x]` bindings are simpler and preferred when they are enough.

**A custom attribute directive.** This one highlights an element on hover:

```ts
import { Directive, ElementRef, HostListener, inject, input } from '@angular/core';

@Directive({
  selector: '[appHighlight]',
})
export class HighlightDirective {
  color = input('yellow', { alias: 'appHighlight' });
  private el = inject<ElementRef<HTMLElement>>(ElementRef);

  @HostListener('mouseenter')
  onEnter(): void {
    this.el.nativeElement.style.backgroundColor = this.color();
  }

  @HostListener('mouseleave')
  onLeave(): void {
    this.el.nativeElement.style.backgroundColor = '';
  }
}
```

Use it by importing the directive into your component and writing:

```html
<p appHighlight="lightblue">Hover me</p>
```

The selector `[appHighlight]` means "any element with this attribute." `@HostListener` listens to events on the **host element** (the element the directive sits on). The `host` metadata property is a modern alternative: `host: { '(mouseenter)': 'onEnter()' }`.

Legacy structural syntax, which you will see in older code:

```html
<div *ngIf="user; else loading">{{ user.name }}</div>
<ng-template #loading>Loading...</ng-template>
<li *ngFor="let t of tasks; trackBy: trackById">{{ t.title }}</li>
```

The `*` is shorthand for wrapping the element in an `<ng-template>`. You do not need to write that yourself.

---

## 4.4 Pipes

A **pipe** transforms a value for display, written with `|`:

```html
<p>{{ today | date: 'longDate' }}</p>
<p>{{ price | currency: 'USD' }}</p>
<p>{{ ratio | percent }}</p>
<p>{{ name | uppercase }}</p>
<p>{{ items | slice: 0 : 3 }}</p>
<pre>{{ user | json }}</pre>
```

Common built-in pipes (from `@angular/common`, imported individually such as `DatePipe`, `CurrencyPipe`):

| Pipe | Result |
|---|---|
| `date` | Formats dates |
| `currency` | Formats money |
| `number`, `percent` | Formats numbers |
| `uppercase`, `lowercase`, `titlecase` | Changes case |
| `slice` | Takes part of an array or string |
| `json` | Pretty-prints an object (great for debugging) |
| `async` | Subscribes to an Observable or Promise (Lesson 13) |

Pipes chain left to right, and arguments follow colons: `{{ today | date: 'short' | uppercase }}`.

**A custom pipe:**

```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'truncate' })
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit = 20): string {
    return value.length > limit ? value.slice(0, limit) + '...' : value;
  }
}
```

```html
<p>{{ description | truncate: 40 }}</p>
```

Remember to add `TruncatePipe` to the component's `imports`.

**Pure vs impure pipes.** By default a pipe is **pure**: Angular reruns it only when its input *reference* changes, which is fast. If you mutate an array in place (`items.push(x)`), a pure pipe will not rerun. Prefer creating a new array, or computing the value in the class with `computed()` (Lesson 6). Setting `pure: false` forces a rerun on every change-detection pass and can hurt performance.

---

**Further reading:** [Templates guide](https://angular.dev/guide/templates) · [Directives guide](https://angular.dev/guide/directives)

[Previous](./[3]-Components-And-Templates.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[5]-Component-Communication.md)
