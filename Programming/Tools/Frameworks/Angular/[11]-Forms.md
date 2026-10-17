[Previous](./[10]-Routing-And-Navigation.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[12]-HTTP-And-Interceptors.md)

*Building Applications*

# Lesson 11 - Forms

Forms collect user input and check that it is valid. Angular offers two approaches, **template-driven** and **reactive**. This lesson teaches both, plus validation and forms whose structure changes at runtime.

---

## 11.1 Template-driven forms

In a template-driven form, the **template** holds most of the logic. It is quick for small forms.

```ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-contact',
  imports: [FormsModule],
  template: `
    <form #f="ngForm" (ngSubmit)="send(f.value)">
      <input name="email" [(ngModel)]="model.email" required email #email="ngModel" />
      @if (email.invalid && email.touched) {
        <small>Enter a valid email.</small>
      }

      <textarea name="message" [(ngModel)]="model.message" required minlength="10"></textarea>

      <button type="submit" [disabled]="f.invalid">Send</button>
    </form>
  `,
})
export class ContactComponent {
  model = { email: '', message: '' };

  send(value: unknown): void {
    console.log('Sending', value);
  }
}
```

What is happening:

- `ngModel` connects an input to your data; **each input needs a `name`** inside a `<form>`.
- `#f="ngForm"` gives you the form's state object. `#email="ngModel"` gives one control's state.
- HTML attributes `required`, `email`, `minlength` become validators.
- `ngSubmit` fires when the form is submitted.

Every control exposes status flags, used by both approaches:

| Flag | Meaning |
|---|---|
| `valid` / `invalid` | Passes / fails validators |
| `touched` / `untouched` | Has / has not lost focus |
| `dirty` / `pristine` | Value has / has not been changed by the user |

Show errors only when `touched` (or after submit), otherwise users see red warnings before they type anything. Template-driven forms are harder to unit-test and to scale, so most teams use reactive forms for anything non-trivial.

---

## 11.2 Reactive forms

In a **reactive** form you build the form model in the **class**, then connect it to the template. This is more explicit, easier to test, and better for complex forms.

```ts
import { Component, inject } from '@angular/core';
import { ReactiveFormsModule, FormBuilder, Validators } from '@angular/forms';

@Component({
  selector: 'app-signup',
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="submit()">
      <label>Email <input formControlName="email" type="email" /></label>
      <label>Password <input formControlName="password" type="password" /></label>

      @if (form.controls.email.touched && form.controls.email.errors?.['email']) {
        <small>Invalid email format.</small>
      }

      <button type="submit" [disabled]="form.invalid">Sign up</button>
    </form>
  `,
})
export class SignupComponent {
  private fb = inject(FormBuilder);

  form = this.fb.nonNullable.group({
    email: ['', [Validators.required, Validators.email]],
    password: ['', [Validators.required, Validators.minLength(8)]],
  });

  submit(): void {
    if (this.form.invalid) return;
    const { email, password } = this.form.getRawValue();   // typed values
    console.log(email, password);
  }
}
```

Building blocks:

| Class | Represents |
|---|---|
| `FormControl` | One value (an input) |
| `FormGroup` | A set of named controls |
| `FormArray` | A list of controls or groups |
| `FormBuilder` | A helper that creates the above with less typing |

Handy APIs:

```ts
form.controls.email.value            // read
form.patchValue({ email: 'a@b.c' })  // set some values
form.reset()                         // clear
form.valueChanges.subscribe(...)     // react to every change
form.controls.email.disable()        // disable one control
```

`nonNullable` keeps values as `string` instead of `string | null` after a reset. Forms are **typed**: `form.controls.email` knows it is a `FormControl<string>`.

> **Signal Forms** are a newer, signal-based forms API (`@angular/forms/signals`). At the time of writing they were still maturing, so check the Angular docs for their current status before using them in production. Reactive forms remain fully supported.

---

## 11.3 Built-in and custom validators

**Built-in validators** live on the `Validators` class: `required`, `minLength`, `maxLength`, `min`, `max`, `email`, `pattern`.

```ts
age: [null, [Validators.required, Validators.min(18), Validators.max(120)]],
zip: ['', Validators.pattern(/^\d{5}$/)],
```

A validator is just a function that takes a control and returns `null` (valid) or an **errors object** (invalid):

```ts
import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

export function forbiddenWord(word: string): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const value = String(control.value ?? '').toLowerCase();
    return value.includes(word) ? { forbiddenWord: { word } } : null;
  };
}

username: ['', [Validators.required, forbiddenWord('admin')]],
```

Display the message:

```html
@if (form.controls.username.errors?.['forbiddenWord']; as err) {
  <small>"{{ err.word }}" is not allowed.</small>
}
```

**Cross-field validators** attach to the *group* and compare several controls:

```ts
const passwordsMatch: ValidatorFn = (group) => {
  const pw = group.get('password')?.value;
  const confirm = group.get('confirm')?.value;
  return pw === confirm ? null : { mismatch: true };
};

form = this.fb.nonNullable.group(
  { password: [''], confirm: [''] },
  { validators: passwordsMatch },
);
```

**Async validators** return an Observable or Promise, e.g. checking whether a username is taken via an API. They run only after synchronous validators pass, and they set the control's status to `pending` while waiting.

```ts
username: ['', { validators: [Validators.required], asyncValidators: [uniqueUsername], updateOn: 'blur' }]
```

`updateOn: 'blur'` avoids calling the server on every keystroke.

---

## 11.4 Dynamic forms

Sometimes a form's shape is not known in advance: a list of phone numbers, or questions loaded from a server.

**Repeating groups with `FormArray`:**

```ts
@Component({
  selector: 'app-skills',
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form">
      <div formArrayName="skills">
        @for (skill of skills.controls; track skill; let i = $index) {
          <div>
            <input [formControlName]="i" />
            <button type="button" (click)="remove(i)">Remove</button>
          </div>
        }
      </div>
      <button type="button" (click)="add()">Add skill</button>
    </form>
  `,
})
export class SkillsComponent {
  private fb = inject(FormBuilder);

  form = this.fb.group({
    skills: this.fb.array([this.fb.control('Angular')]),
  });

  get skills() { return this.form.controls.skills; }

  add(): void { this.skills.push(this.fb.control('')); }
  remove(i: number): void { this.skills.removeAt(i); }
}
```

**Forms generated from a description.** Define the fields as data, then build controls in a loop:

```ts
interface Field { key: string; label: string; required?: boolean; type: 'text' | 'number'; }

fields: Field[] = [
  { key: 'name', label: 'Name', required: true, type: 'text' },
  { key: 'age', label: 'Age', type: 'number' },
];

form = new FormGroup(
  Object.fromEntries(
    this.fields.map((f) => [
      f.key,
      new FormControl('', f.required ? [Validators.required] : []),
    ]),
  ),
);
```

```html
<form [formGroup]="form">
  @for (f of fields; track f.key) {
    <label>{{ f.label }} <input [type]="f.type" [formControlName]="f.key" /></label>
  }
</form>
```

Useful patterns:

- Add or remove controls at runtime with `addControl`, `removeControl`, and `setValidators` followed by `updateValueAndValidity()`.
- Show a section only when a checkbox is ticked by subscribing to `valueChanges` and enabling or disabling that group.
- For very large dynamic forms, split sections into child components that receive a `FormGroup` through an input.

---

**Further reading:** [Forms guide](https://angular.dev/guide/forms)

[Previous](./[10]-Routing-And-Navigation.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[12]-HTTP-And-Interceptors.md)
