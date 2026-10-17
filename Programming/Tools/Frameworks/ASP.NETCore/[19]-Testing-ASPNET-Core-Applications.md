[Previous](./[18]-Web-API-Design.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[20]-Performance-And-Caching.md)

*Quality And Delivery*

# Lesson 19 - Testing ASP.NET Core Applications

## 19.1 xUnit basics

Automated tests verify that your code works and keeps working. The main kinds:

| Type | Tests | Speed | Dependencies |
|---|---|---|---|
| **Unit tests** | One class/method in isolation | Very fast | Faked/mocked |
| **Integration tests** | Several parts together (HTTP pipeline, database) | Slower | Real or near-real |
| **End-to-end (UI) tests** | The whole system via a browser | Slowest | Everything |

**xUnit** is the most commonly used .NET test framework (NUnit and MSTest are alternatives).

**Create a test project:**

```bash
dotnet new xunit -n MyApp.Tests
cd MyApp.Tests
dotnet add reference ../MyApp/MyApp.csproj
dotnet test                         # run from the solution or test folder
```

**Structure:**

```
MyApp.sln
├── MyApp/              ← the application
└── MyApp.Tests/        ← the tests (a separate project that references MyApp)
```

**Writing tests:**

```csharp
public class Calculator
{
    public int Add(int a, int b) => a + b;
    public int Divide(int a, int b) => b == 0 ? throw new DivideByZeroException() : a / b;
}

public class CalculatorTests
{
    [Fact]                                           // a single test, no parameters
    public void Add_TwoNumbers_ReturnsSum()
    {
        // Arrange
        var calc = new Calculator();

        // Act
        var result = calc.Add(2, 3);

        // Assert
        Assert.Equal(5, result);
    }

    [Theory]                                         // the same test with several data sets
    [InlineData(1, 1, 2)]
    [InlineData(0, 5, 5)]
    [InlineData(-1, 1, 0)]
    public void Add_Various(int a, int b, int expected)
        => Assert.Equal(expected, new Calculator().Add(a, b));

    [Fact]
    public void Divide_ByZero_Throws()
        => Assert.Throws<DivideByZeroException>(() => new Calculator().Divide(1, 0));
}
```

**Arrange-Act-Assert (AAA)** keeps tests readable: set up, perform the action, check the result.

**Common assertions:**

| Assertion | Purpose |
|---|---|
| `Assert.Equal(expected, actual)` | Values equal |
| `Assert.True(x)`, `Assert.False(x)` | Boolean conditions |
| `Assert.Null(x)`, `Assert.NotNull(x)` | Null checks |
| `Assert.Contains(item, collection)` | Membership |
| `Assert.Empty(collection)`, `Assert.Single(collection)` | Collection size |
| `Assert.Throws<T>(...)` / `await Assert.ThrowsAsync<T>(...)` | Exceptions |
| `Assert.IsType<T>(obj)` | Runtime type |

**Naming:** `Method_Scenario_ExpectedResult` makes failures self-explanatory. Each test should be independent: xUnit creates a **new instance of the test class for every test**, so use the constructor for setup and `IDisposable` for cleanup.

**Async tests** return `Task`:

```csharp
[Fact]
public async Task GetAsync_ReturnsData()
{
    var result = await service.GetAsync(1);
    Assert.NotNull(result);
}
```

---

## 19.2 Unit tests

A unit test checks one class while its **dependencies are replaced** with fakes. DI (Lesson 13) is what makes this easy: pass fakes through the constructor.

**Code under test:**

```csharp
public interface IProductRepository
{
    Task<Product?> FindAsync(int id);
}

public class PriceService
{
    private readonly IProductRepository _repo;
    public PriceService(IProductRepository repo) => _repo = repo;

    public async Task<decimal> GetPriceWithTaxAsync(int productId, decimal taxRate)
    {
        var product = await _repo.FindAsync(productId)
                      ?? throw new KeyNotFoundException($"Product {productId} not found.");
        return Math.Round(product.Price * (1 + taxRate), 2);
    }
}
```

**A test with a hand-written fake:**

```csharp
public class FakeProductRepository : IProductRepository
{
    private readonly Dictionary<int, Product> _data = new()
    {
        [1] = new Product { Id = 1, Name = "Pen", Price = 10m }
    };

    public Task<Product?> FindAsync(int id)
        => Task.FromResult(_data.GetValueOrDefault(id));
}

public class PriceServiceTests
{
    [Fact]
    public async Task GetPriceWithTax_AddsTax()
    {
        var service = new PriceService(new FakeProductRepository());

        var price = await service.GetPriceWithTaxAsync(1, 0.2m);

        Assert.Equal(12.00m, price);
    }

    [Fact]
    public async Task GetPriceWithTax_UnknownProduct_Throws()
    {
        var service = new PriceService(new FakeProductRepository());

        await Assert.ThrowsAsync<KeyNotFoundException>(
            () => service.GetPriceWithTaxAsync(99, 0.2m));
    }
}
```

**Testing controllers / endpoints.** Because controllers are plain classes, instantiate them with fakes:

```csharp
[Fact]
public async Task GetById_Missing_ReturnsNotFound()
{
    var controller = new ProductsController(new FakeProductRepository());

    var result = await controller.GetById(404);

    Assert.IsType<NotFoundResult>(result.Result);
}
```

For minimal APIs, move the handler into a static method returning `TypedResults` (Lesson 5) so you can call it and assert on the returned type, e.g. `Assert.IsType<NotFound>(result.Result)`.

**Testing code that uses EF Core.** Options, from least to most realistic:

| Approach | Notes |
|---|---|
| EF Core **InMemory** provider | Easy, but doesn't behave like a relational database (no constraints/transactions); not recommended for serious tests |
| **SQLite in-memory** | Relational behavior, fast, good for most tests |
| **Testcontainers** with real SQL Server/PostgreSQL | Most realistic; slower; needs Docker |

```csharp
// SQLite in-memory test database
var connection = new SqliteConnection("DataSource=:memory:");
connection.Open();                                       // the DB lives as long as the connection

var options = new DbContextOptionsBuilder<AppDbContext>().UseSqlite(connection).Options;
using var db = new AppDbContext(options);
db.Database.EnsureCreated();
```

**Principles:** test behavior (inputs → outputs) not implementation details; one reason to fail per test; avoid logic (loops, ifs) in tests; keep them fast and deterministic (no real clock, network, or random values without control).

---

## 19.3 Integration tests with WebApplicationFactory

Integration tests start your **real app** (in memory, using a test server) and send **real HTTP requests** through the entire pipeline: routing, middleware, model binding, serialization, validation, and so on.

**Setup:**

```bash
dotnet add package Microsoft.AspNetCore.Mvc.Testing
```

The test project must use the web SDK, so change its `.csproj` first line to `<Project Sdk="Microsoft.NET.Sdk.Web">` (or add `<FrameworkReference Include="Microsoft.AspNetCore.App" />`) if the template doesn't already handle it.

**Make `Program` visible to tests.** With top-level statements, the `Program` class is generated as internal. Add this at the bottom of `Program.cs` in the app:

```csharp
public partial class Program { }
```

**A basic test:**

```csharp
using System.Net;
using System.Net.Http.Json;
using Microsoft.AspNetCore.Mvc.Testing;

public class ProductsApiTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public ProductsApiTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();            // an HttpClient wired to the in-memory server
    }

    [Fact]
    public async Task GetProducts_ReturnsOk()
    {
        var response = await _client.GetAsync("/api/products");

        response.EnsureSuccessStatusCode();
        var items = await response.Content.ReadFromJsonAsync<List<ProductDto>>();
        Assert.NotNull(items);
    }

    [Fact]
    public async Task GetProduct_Unknown_Returns404()
    {
        var response = await _client.GetAsync("/api/products/99999");
        Assert.Equal(HttpStatusCode.NotFound, response.StatusCode);
    }

    [Fact]
    public async Task CreateProduct_InvalidBody_Returns400()
    {
        var response = await _client.PostAsJsonAsync("/api/products", new { Name = "", Price = -1 });
        Assert.Equal(HttpStatusCode.BadRequest, response.StatusCode);
    }
}
```

`IClassFixture<T>` creates the factory **once per test class** and shares it across its tests.

**Replacing services for tests** (use a test database, fake external APIs, bypass auth) by deriving a custom factory:

```csharp
public class TestAppFactory : WebApplicationFactory<Program>
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment("Testing");

        builder.ConfigureTestServices(services =>
        {
            // Remove the real DbContext registration
            var descriptor = services.SingleOrDefault(
                d => d.ServiceType == typeof(DbContextOptions<AppDbContext>));
            if (descriptor is not null) services.Remove(descriptor);

            // Use SQLite in-memory or a test container instead
            services.AddDbContext<AppDbContext>(o => o.UseSqlite("DataSource=file::memory:?cache=shared"));

            // Replace an external dependency with a fake
            services.AddSingleton<IEmailSender, FakeEmailSender>();
        });
    }
}

public class OrdersApiTests : IClassFixture<TestAppFactory> { ... }
```

(`ConfigureTestServices` comes from `Microsoft.AspNetCore.TestHost`.)

**Testing authenticated endpoints:** register a test authentication handler (a fake scheme that always signs in a test user), or generate a valid JWT using the test configuration.

**Tips:** reset database state between tests (new database per class, or transactions/respawn tools); don't depend on test execution order; run integration tests in CI.

---

## 19.4 Mocking

A **mock** (or *test double*) is a stand-in for a dependency that you can configure to return specific values and then **verify** was called as expected. Libraries generate them from interfaces so you don't write fake classes by hand.

Popular libraries: **Moq**, **NSubstitute**, **FakeItEasy**. Pick one that fits your team and check each library's current license/maintenance status.

**Moq example:**

```bash
dotnet add package Moq
```

```csharp
using Moq;

public class OrderServiceTests
{
    [Fact]
    public async Task PlaceOrder_SendsConfirmationEmail()
    {
        // Arrange
        var email = new Mock<IEmailSender>();
        var repo  = new Mock<IOrderRepository>();
        repo.Setup(r => r.SaveAsync(It.IsAny<Order>())).ReturnsAsync(42);        // stubbed return value

        var service = new OrderService(repo.Object, email.Object);

        // Act
        await service.PlaceOrderAsync(new Order { CustomerEmail = "a@b.com" });

        // Assert: verify the interaction
        email.Verify(e => e.SendAsync("a@b.com", It.IsAny<string>(), It.IsAny<string>()), Times.Once);
        repo.Verify(r => r.SaveAsync(It.IsAny<Order>()), Times.Once);
    }

    [Fact]
    public async Task PlaceOrder_RepositoryFails_DoesNotSendEmail()
    {
        var email = new Mock<IEmailSender>();
        var repo  = new Mock<IOrderRepository>();
        repo.Setup(r => r.SaveAsync(It.IsAny<Order>())).ThrowsAsync(new InvalidOperationException());

        var service = new OrderService(repo.Object, email.Object);

        await Assert.ThrowsAsync<InvalidOperationException>(
            () => service.PlaceOrderAsync(new Order { CustomerEmail = "a@b.com" }));

        email.Verify(e => e.SendAsync(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>()), Times.Never);
    }
}
```

**The same idea with NSubstitute:**

```csharp
var email = Substitute.For<IEmailSender>();
var repo  = Substitute.For<IOrderRepository>();
repo.SaveAsync(Arg.Any<Order>()).Returns(42);

await service.PlaceOrderAsync(order);

await email.Received(1).SendAsync("a@b.com", Arg.Any<string>(), Arg.Any<string>());
```

**Vocabulary:**

| Term | Meaning |
|---|---|
| **Stub** | Returns canned answers |
| **Fake** | A simple working implementation (e.g. in-memory repository) |
| **Mock** | Records calls so you can verify interactions |

**Guidelines:**

- Mock what you **own the interface for** (repositories, gateways, clocks). Avoid mocking types you don't own (like `DbContext` or `HttpClient`). Wrap them, or use SQLite / `HttpMessageHandler` fakes instead.
- Don't over-verify: asserting every call makes tests brittle. Verify outcomes first, and interactions only when they *are* the outcome (an email was sent).
- Too many mocks in one test is a sign the class has too many dependencies.
- Inject `TimeProvider` (built into .NET 8+) instead of calling `DateTime.UtcNow` so time-dependent code is testable.

---

[Previous](./[18]-Web-API-Design.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[20]-Performance-And-Caching.md)
