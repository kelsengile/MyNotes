[Previous](./[9]-Model-Binding-And-Validation.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[11]-Entity-Framework-Core.md)

*Server-Side Development*

# Lesson 10 - Blazor Components

## 10.1 Blazor hosting models

**Blazor** lets you build interactive web UI with **C# and Razor syntax instead of JavaScript**. The UI is made of reusable **components**. How components run depends on the **render mode**.

| Render mode | Where the code runs | How the UI updates | Notes |
|---|---|---|---|
| **Static SSR** | Server, per request | Full page responses (no interactivity) | Like Razor Pages; fastest start |
| **Interactive Server** | Server | A **SignalR** (WebSocket) connection sends UI diffs to the browser | Small download; needs a live connection; latency per interaction |
| **Interactive WebAssembly** | In the **browser** (.NET compiled to WebAssembly) | Local | Larger first download; works offline; no per-click server round trip |
| **Interactive Auto** | Server first, then WebAssembly after it is downloaded | Both | Fast startup and later offloads to the client |

Historically these were "Blazor Server" and "Blazor WebAssembly" apps. Since .NET 8, the **Blazor Web App** template unifies them: you choose a render mode **per component or page**.

**Create a project:**

```bash
dotnet new blazor -n MyBlazorApp -int Server    # interactive server
# -int options: None, Server, WebAssembly, Auto
cd MyBlazorApp
dotnet run
```

**Setting up interactivity (`Program.cs` of a Blazor Web App):**

```csharp
builder.Services.AddRazorComponents()
    .AddInteractiveServerComponents();          // add .AddInteractiveWebAssemblyComponents() for WASM

var app = builder.Build();
app.UseAntiforgery();
app.MapRazorComponents<App>()
   .AddInteractiveServerRenderMode();
```

**Opting a component into interactivity:**

```razor
@page "/counter"
@rendermode InteractiveServer
```

Without a render mode, a component renders statically: it shows HTML but its `@onclick` handlers will not run.

**Choosing:** use Static SSR for content pages, Interactive Server for internal/line-of-business apps with reliable connections, WebAssembly when you need client-side execution or offline, and Auto for public apps wanting both.

---

## 10.2 Components and parameters

A **component** is a `.razor` file. It combines markup and C# in one file. The file name (PascalCase) is the component name, which you use as an HTML-like tag.

`Components/Pages/Counter.razor`:

```razor
@page "/counter"
@rendermode InteractiveServer

<h1>Counter</h1>
<p>Current count: @currentCount</p>
<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount() => currentCount++;
}
```

- `@page "/counter"` makes it routable (a **page** component). Without it, it's a reusable child component.
- Markup is Razor. The `@code { }` block holds fields, properties, and methods.
- When an event handler runs, Blazor re-renders the component and updates only the DOM differences.

**Parameters** pass data into a child component from its parent. Declare them with `[Parameter]` on a public property:

`Components/ProductCard.razor`:

```razor
<div class="card">
    <h3>@Name</h3>
    <p>@Price.ToString("C")</p>
    @ChildContent
    <button @onclick="() => OnSelected.InvokeAsync(Name)">Select</button>
</div>

@code {
    [Parameter, EditorRequired] public string Name { get; set; } = "";
    [Parameter] public decimal Price { get; set; }
    [Parameter] public RenderFragment? ChildContent { get; set; }     // nested markup
    [Parameter] public EventCallback<string> OnSelected { get; set; } // child → parent event
}
```

Use it from a parent:

```razor
<ProductCard Name="Laptop" Price="999.99" OnSelected="HandleSelected">
    <em>Limited stock</em>
</ProductCard>

@code {
    private void HandleSelected(string name) => Console.WriteLine($"Selected {name}");
}
```

**Data flows down (parameters), events flow up (`EventCallback`).**

**Other useful concepts:**

| Feature | Purpose |
|---|---|
| `[CascadingParameter]` | Receive a value provided by an ancestor (theme, auth state) without passing it manually |
| `@inject IMyService Service` | Dependency injection into a component |
| `@if`, `@foreach` | Control flow in markup |
| `@key` | Helps Blazor track items in a loop |
| `_Imports.razor` | Shared `@using` directives for a folder |

**Lifecycle methods** (override inside `@code`):

| Method | When it runs |
|---|---|
| `OnInitialized` / `OnInitializedAsync` | Once, when the component is created. Load data here. |
| `OnParametersSet` / `OnParametersSetAsync` | Each time parameters may have changed |
| `OnAfterRender` / `OnAfterRenderAsync(bool firstRender)` | After each render. JavaScript interop goes here. |
| `Dispose` (via `@implements IDisposable`) | When the component is removed |

```razor
@inject IProductService Products

@if (items is null)
{
    <p>Loading...</p>
}
else
{
    @foreach (var p in items) { <ProductCard Name="@p.Name" Price="@p.Price" /> }
}

@code {
    private List<Product>? items;

    protected override async Task OnInitializedAsync()
        => items = await Products.GetAllAsync();
}
```

---

## 10.3 Event handling and binding

**Events.** Attach C# handlers to DOM events with `@on{event}`:

```razor
<button @onclick="Save">Save</button>
<button @onclick="() => Delete(item.Id)">Delete</button>
<input @onkeydown="OnKey" />
<div @onmouseover="Hover">Hover me</div>

@code {
    private void Save() { }
    private void Delete(int id) { }
    private void OnKey(KeyboardEventArgs e) => Console.WriteLine(e.Key);
    private void Hover(MouseEventArgs e) { }
}
```

Handlers can be `async Task` as well. Use lambdas to pass arguments. Event argument types include `MouseEventArgs`, `KeyboardEventArgs`, `ChangeEventArgs`.

**Two-way data binding** with `@bind` keeps a field and an input in sync:

```razor
<input @bind="name" />
<p>Hello, @name!</p>

@code {
    private string name = "";
}
```

By default `@bind` updates when the input **loses focus** (the `change` event). To update on every keystroke:

```razor
<input @bind="name" @bind:event="oninput" />
```

Other binding options:

```razor
<input type="checkbox" @bind="isActive" />
<select @bind="selectedColor">
    <option value="red">Red</option>
    <option value="blue">Blue</option>
</select>
<input type="date" @bind="dueDate" @bind:format="yyyy-MM-dd" />
```

**Binding between components** (child edits parent's value): the child exposes a parameter `Value` plus an `EventCallback<T> ValueChanged`, and the parent uses `@bind-Value="field"`:

`NameEditor.razor`:

```razor
<input value="@Value" @oninput="e => ValueChanged.InvokeAsync(e.Value?.ToString())" />

@code {
    [Parameter] public string? Value { get; set; }
    [Parameter] public EventCallback<string?> ValueChanged { get; set; }
}
```

Parent: `<NameEditor @bind-Value="userName" />`

**Manual re-rendering.** Blazor re-renders automatically after events, but when state changes from elsewhere (a timer or an external event), call `StateHasChanged()` (and inside `InvokeAsync(() => StateHasChanged())` if on another thread).

---

## 10.4 Forms and interop

**Forms.** Blazor's `EditForm` ties a model to input components and validation (using the same data annotations as Lesson 9).

```razor
@page "/register"
@rendermode InteractiveServer

<EditForm Model="model" OnValidSubmit="HandleValid" FormName="register">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <div>
        <label>Name</label>
        <InputText @bind-Value="model.Name" />
        <ValidationMessage For="() => model.Name" />
    </div>

    <div>
        <label>Age</label>
        <InputNumber @bind-Value="model.Age" />
        <ValidationMessage For="() => model.Age" />
    </div>

    <div>
        <label>Subscribe</label>
        <InputCheckbox @bind-Value="model.Subscribe" />
    </div>

    <button type="submit">Register</button>
</EditForm>

@code {
    private RegisterModel model = new();

    private void HandleValid() => Console.WriteLine($"Registered {model.Name}");

    public class RegisterModel
    {
        [Required, StringLength(50)] public string Name { get; set; } = "";
        [Range(18, 120)] public int Age { get; set; }
        public bool Subscribe { get; set; }
    }
}
```

- `OnValidSubmit` runs only if validation passes. `OnInvalidSubmit` and `OnSubmit` exist too.
- Built-in inputs: `InputText`, `InputTextArea`, `InputNumber<T>`, `InputCheckbox`, `InputDate<T>`, `InputSelect<T>`, `InputRadioGroup<T>`, `InputFile`.
- `FormName` is needed when the form is used with **static SSR** (and `[SupplyParameterFromForm]` binds the model on post). With interactive render modes, you can use the example above.

**JavaScript interop.** Blazor can call JavaScript, and JavaScript can call C#, for browser APIs you can't reach from C# (local storage, focus, maps, charts).

Add an ES module in `wwwroot/js/interop.js`:

```javascript
export function showAlert(message) { alert(message); }
export function focusElement(element) { element.focus(); }
```

Import it with `IJSRuntime` and call its functions:

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<button @onclick="Hello">Say hello</button>
<input @ref="inputRef" />

@code {
    private ElementReference inputRef;
    private IJSObjectReference? module;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            module = await JS.InvokeAsync<IJSObjectReference>("import", "./js/interop.js");
            await module.InvokeVoidAsync("focusElement", inputRef);
        }
    }

    private async Task Hello()
    {
        if (module is not null)
            await module.InvokeVoidAsync("showAlert", "Hello from C#!");
    }

    public async ValueTask DisposeAsync()
    {
        if (module is not null) await module.DisposeAsync();
    }
}
```

Rules to remember:

- Call JavaScript only **after the first render** (in `OnAfterRenderAsync`), because during server-side prerendering no browser exists yet.
- `InvokeVoidAsync` calls with no return value; `InvokeAsync<T>` returns a value.
- JavaScript can call back into .NET using methods marked `[JSInvokable]`.
- With Interactive Server, every JS call is a network round trip, so batch calls where possible.

---

[Previous](./[9]-Model-Binding-And-Validation.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[11]-Entity-Framework-Core.md)
