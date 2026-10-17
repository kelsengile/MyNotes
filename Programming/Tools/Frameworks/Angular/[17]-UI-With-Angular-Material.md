[Previous](./[16]-Performance-And-Change-Detection.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[18]-Building-And-Deployment.md)

*Quality And Delivery*

# Lesson 17 - UI With Angular Material

Angular Material is the official UI component library built on Google's Material Design. It provides ready-made buttons, forms, dialogs, tables, and more, plus the **CDK** for building your own components. This lesson also covers theming, accessibility, and translation.

---

## 17.1 Material components

Install and follow the prompts (theme, typography):

```bash
ng add @angular/material
```

Each component is a standalone module you import where needed:

```ts
import { MatButtonModule } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';
import { MatFormFieldModule } from '@angular/material/form-field';
import { MatInputModule } from '@angular/material/input';

@Component({
  selector: 'app-login-card',
  imports: [MatButtonModule, MatCardModule, MatFormFieldModule, MatInputModule, ReactiveFormsModule],
  template: `
    <mat-card>
      <mat-card-header><mat-card-title>Sign in</mat-card-title></mat-card-header>
      <mat-card-content>
        <mat-form-field appearance="outline">
          <mat-label>Email</mat-label>
          <input matInput type="email" [formControl]="email" />
          @if (email.hasError('required')) { <mat-error>Email is required</mat-error> }
        </mat-form-field>
      </mat-card-content>
      <mat-card-actions>
        <button mat-flat-button type="button">Sign in</button>
      </mat-card-actions>
    </mat-card>
  `,
})
export class LoginCardComponent {
  email = new FormControl('', { nonNullable: true, validators: [Validators.required] });
}
```

Frequently used components:

| Area | Components |
|---|---|
| Inputs | `mat-form-field`, `matInput`, `mat-select`, `mat-checkbox`, `mat-datepicker` |
| Buttons | `mat-button`, `mat-flat-button`, `mat-raised-button`, `mat-icon-button` |
| Layout | `mat-toolbar`, `mat-sidenav`, `mat-card`, `mat-tab-group`, `mat-list` |
| Data | `mat-table`, `mat-paginator`, `mat-sort` |
| Feedback | `MatDialog`, `MatSnackBar`, `mat-progress-bar`, `mat-spinner` |

**Opening a dialog and a snackbar from code:**

```ts
private dialog = inject(MatDialog);
private snack = inject(MatSnackBar);

confirmDelete(): void {
  this.dialog.open(ConfirmDialogComponent, { data: { name: 'Task 1' } })
    .afterClosed()
    .subscribe((ok) => ok && this.snack.open('Deleted', 'Undo', { duration: 3000 }));
}
```

`MatDialog` and `MatSnackBar` come from `@angular/material/dialog` and `@angular/material/snack-bar`. Icons use the Material Icons font: `<mat-icon>home</mat-icon>`.

---

## 17.2 The CDK

The **Component Dev Kit** (`@angular/cdk`) holds the **behavior** that Material components are built on, without Material's visual style. Use it when you need accessibility and logic but a custom look.

Notable parts:

| Area | What it gives you |
|---|---|
| `overlay` | Floating panels (menus, tooltips, popovers) positioned relative to an element |
| `a11y` | Focus trapping, focus monitoring, live announcer |
| `drag-drop` | Draggable items and sortable lists |
| `scrolling` | Virtual scroll for long lists |
| `layout` | `BreakpointObserver` for responsive logic |
| `clipboard`, `portal`, `table`, `tree` | Utilities and low-level building blocks |

**Sortable list with drag and drop:**

```ts
import { CdkDragDrop, CdkDrag, CdkDropList, moveItemInArray } from '@angular/cdk/drag-drop';

@Component({
  selector: 'app-sortable',
  imports: [CdkDropList, CdkDrag],
  template: `
    <div cdkDropList (cdkDropListDropped)="drop($event)">
      @for (item of items; track item) {
        <div cdkDrag>{{ item }}</div>
      }
    </div>
  `,
})
export class SortableComponent {
  items = ['Plan', 'Build', 'Test', 'Ship'];

  drop(event: CdkDragDrop<string[]>): void {
    moveItemInArray(this.items, event.previousIndex, event.currentIndex);
  }
}
```

**Responsive logic with `BreakpointObserver`:**

```ts
import { BreakpointObserver, Breakpoints } from '@angular/cdk/layout';

isHandset = toSignal(
  inject(BreakpointObserver).observe(Breakpoints.Handset).pipe(map((r) => r.matches)),
  { initialValue: false },
);
```

You can use the CDK without Material at all, and it works well for company design systems.

---

## 17.3 Theming

Material 3 themes are defined in Sass in your global stylesheet (often `src/styles.scss`). A theme is a set of **design tokens**: colors, typography, and density.

```scss
@use '@angular/material' as mat;

html {
  color-scheme: light dark;            // follow the user's OS setting

  @include mat.theme((
    color: mat.$violet-palette,
    typography: Roboto,
    density: 0
  ));
}
```

Elements:

- **Color palette** controls primary and related tones; Material ships premade palettes such as azure, violet, and rose.
- **Typography** sets the font family.
- **Density** shrinks or expands spacing (`0` default, negative values are more compact).

Material exposes **CSS custom properties** (system tokens) you can use in your own CSS so your components match:

```css
.panel {
  background: var(--mat-sys-surface-container);
  color: var(--mat-sys-on-surface);
  border-radius: var(--mat-sys-corner-medium);
}
```

To tweak a single component, override its tokens with the component's Sass mixin or CSS variables rather than writing deep selectors:

```scss
.danger-button {
  --mat-button-filled-container-color: #b3261e;
}
```

Because exact mixin and token names change between Material versions, consult the theming guide for your installed version at [material.angular.dev](https://material.angular.dev) when customizing.

---

## 17.4 Accessibility and internationalization

**Accessibility (a11y)** means everyone, including keyboard and screen-reader users, can use your app. Material and CDK handle many details, but you are still responsible for:

- **Labels:** every input needs a label (`<mat-label>`, `aria-label`, or `<label for>`). Icon-only buttons need `aria-label`:

  ```html
  <button mat-icon-button aria-label="Delete task"><mat-icon>delete</mat-icon></button>
  ```

- **Semantic HTML:** use `<button>`, `<nav>`, `<main>`, headings in order; avoid clickable `<div>`s.
- **Keyboard:** everything clickable must be reachable and usable with Tab/Enter/Space; keep a visible focus outline.
- **Contrast and color:** do not use color as the only signal; keep contrast high.
- **Announcements:** tell screen readers about dynamic changes:

  ```ts
  inject(LiveAnnouncer).announce('Task deleted');   // from @angular/cdk/a11y
  ```

Test with a keyboard only, then with a screen reader, and use automated checks such as Lighthouse or axe.

**Internationalization (i18n)** prepares your app for multiple languages. Angular's built-in approach translates at **build time**:

1. Mark text in templates:

   ```html
   <h1 i18n="@@welcomeTitle">Welcome to our store</h1>
   <img src="logo.png" i18n-alt alt="Company logo" />
   ```

2. In code, use `$localize`:

   ```ts
   const msg = $localize`:@@greeting:Hello, ${this.name}!`;
   ```

3. Extract text: `ng extract-i18n` creates a translation file (XLIFF or other formats).
4. Translators produce one file per language; configure locales in `angular.json` and run `ng build --localize` to get one app build per language.

Locale-aware pipes (`date`, `currency`, `number`) format values correctly once the locale is registered. If you prefer **runtime** language switching without separate builds, community libraries such as Transloco or ngx-translate are popular alternatives.

---

**Further reading:** [Angular Material](https://material.angular.dev)

[Previous](./[16]-Performance-And-Change-Detection.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[18]-Building-And-Deployment.md)
