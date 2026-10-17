[Previous](./[21]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md)

*Quality And Delivery*

# Lesson 22 - Next Steps With ASP.NET Core

## 22.1 gRPC and SignalR

ASP.NET Core supports communication styles beyond request/response REST.

### gRPC

**gRPC** is a high-performance remote procedure call framework that uses **HTTP/2** and **Protocol Buffers** (a compact binary format). Clients call methods on a server as if they were local functions, with strongly typed contracts. It suits **service-to-service communication** in microservices and streaming scenarios. Browsers cannot call gRPC directly without gRPC-Web.

**1. Define the contract** in `Protos/greeter.proto`:

```protobuf
syntax = "proto3";

option csharp_namespace = "MyApp.Grpc";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
  rpc StreamNumbers (NumberRequest) returns (stream NumberReply);   // server streaming
}

message HelloRequest { string name = 1; }
message HelloReply   { string message = 1; }
message NumberRequest { int32 count = 1; }
message NumberReply   { int32 value = 1; }
```

**2. Add the package and register the proto** (`dotnet add package Grpc.AspNetCore`):

```xml
<ItemGroup>
  <Protobuf Include="Protos\greeter.proto" GrpcServices="Server" />
</ItemGroup>
```

**3. Implement the service:**

```csharp
using Grpc.Core;
using MyApp.Grpc;

public class GreeterService : Greeter.GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
        => Task.FromResult(new HelloReply { Message = $"Hello {request.Name}" });

    public override async Task StreamNumbers(
        NumberRequest request, IServerStreamWriter<NumberReply> stream, ServerCallContext context)
    {
        for (int i = 1; i <= request.Count && !context.CancellationToken.IsCancellationRequested; i++)
        {
            await stream.WriteAsync(new NumberReply { Value = i });
            await Task.Delay(500, context.CancellationToken);
        }
    }
}

// Program.cs
builder.Services.AddGrpc();
app.MapGrpcService<GreeterService>();
```

A .NET client uses `Grpc.Net.Client` with a generated client class:

```csharp
using var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);
var reply = await client.SayHelloAsync(new HelloRequest { Name = "Alice" });
```

### SignalR

**SignalR** provides **real-time, two-way communication** between server and browsers (chat, live dashboards, notifications, collaborative editing). It automatically picks the best transport (WebSockets, Server-Sent Events, or long polling).

**1. A hub:**

```csharp
using Microsoft.AspNetCore.SignalR;

public class ChatHub : Hub
{
    public async Task SendMessage(string user, string message)
        => await Clients.All.SendAsync("ReceiveMessage", user, message);   // broadcast to everyone

    public async Task JoinRoom(string room)
    {
        await Groups.AddToGroupAsync(Context.ConnectionId, room);
        await Clients.Group(room).SendAsync("ReceiveMessage", "system", $"{Context.ConnectionId} joined");
    }
}
```

**2. Register:**

```csharp
builder.Services.AddSignalR();
app.MapHub<ChatHub>("/chat");
```

**3. A JavaScript client** (`npm install @microsoft/signalr`):

```javascript
import * as signalR from "@microsoft/signalr";

const connection = new signalR.HubConnectionBuilder()
  .withUrl("/chat")
  .withAutomaticReconnect()
  .build();

connection.on("ReceiveMessage", (user, message) => console.log(`${user}: ${message}`));

await connection.start();
await connection.invoke("SendMessage", "Alice", "Hello!");
```

**Sending from outside a hub** (e.g. from an endpoint or a background service) by injecting `IHubContext<ChatHub>`:

```csharp
app.MapPost("/notify", async (IHubContext<ChatHub> hub, string text) =>
{
    await hub.Clients.All.SendAsync("ReceiveMessage", "server", text);
    return Results.Ok();
});
```

**Scaling:** with several server instances, SignalR needs a **backplane** (Redis, or Azure SignalR Service) so messages reach clients connected to other servers. Authentication works like the rest of the app (`[Authorize]` on a hub); browsers often send the JWT as a query-string `access_token` for WebSocket connections.

| | gRPC | SignalR | REST |
|---|---|---|---|
| Best for | Service-to-service, streaming | Browser real-time push | Public/general APIs |
| Format | Protobuf (binary) | JSON/MessagePack | JSON |
| Browser support | Needs gRPC-Web | Native | Native |

---

## 22.2 Background services

Many apps need work to run **outside of a request**: sending queued emails, cleaning old data, polling an API, processing messages. ASP.NET Core runs this inside the same process using **hosted services**.

**`BackgroundService`** is a base class with a single method to implement, `ExecuteAsync`, which runs for the lifetime of the app:

```csharp
public class CleanupWorker : BackgroundService
{
    private readonly IServiceScopeFactory _scopeFactory;
    private readonly ILogger<CleanupWorker> _logger;

    public CleanupWorker(IServiceScopeFactory scopeFactory, ILogger<CleanupWorker> logger)
    {
        _scopeFactory = scopeFactory;
        _logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        using var timer = new PeriodicTimer(TimeSpan.FromHours(1));

        // Run once per hour until the app shuts down
        while (await timer.WaitForNextTickAsync(stoppingToken))
        {
            try
            {
                using var scope = _scopeFactory.CreateScope();      // scoped services need a manual scope
                var db = scope.ServiceProvider.GetRequiredService<AppDbContext>();

                var cutoff = DateTime.UtcNow.AddDays(-30);
                var removed = await db.Sessions.Where(s => s.LastSeen < cutoff)
                                               .ExecuteDeleteAsync(stoppingToken);
                _logger.LogInformation("Removed {Count} stale sessions", removed);
            }
            catch (OperationCanceledException) { /* shutting down */ }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Cleanup failed");              // never let the loop crash silently
            }
        }
    }
}

// Program.cs
builder.Services.AddHostedService<CleanupWorker>();
```

**Key rules:**

- A hosted service is effectively a **singleton**. Create a scope with `IServiceScopeFactory` to use scoped services like `DbContext` (Lesson 13).
- **Honor the `CancellationToken`** so shutdown is graceful.
- **Catch exceptions** inside the loop. Since .NET 6 an unhandled exception in a `BackgroundService` stops the host by default.
- Startup of other services does not wait for `ExecuteAsync` to finish its work; the first `await` returns control to the host.

**A background queue** pattern: endpoints enqueue work and return immediately; a worker processes it, using `System.Threading.Channels`:

```csharp
public class WorkQueue
{
    private readonly Channel<Func<CancellationToken, Task>> _channel =
        Channel.CreateUnbounded<Func<CancellationToken, Task>>();

    public ValueTask EnqueueAsync(Func<CancellationToken, Task> work) => _channel.Writer.WriteAsync(work);
    public IAsyncEnumerable<Func<CancellationToken, Task>> DequeueAllAsync(CancellationToken ct)
        => _channel.Reader.ReadAllAsync(ct);
}

public class QueueWorker : BackgroundService
{
    private readonly WorkQueue _queue;
    public QueueWorker(WorkQueue queue) => _queue = queue;

    protected override async Task ExecuteAsync(CancellationToken ct)
    {
        await foreach (var work in _queue.DequeueAllAsync(ct))
            await work(ct);
    }
}

builder.Services.AddSingleton<WorkQueue>();
builder.Services.AddHostedService<QueueWorker>();

app.MapPost("/report", async (WorkQueue queue) =>
{
    await queue.EnqueueAsync(async ct => await Task.Delay(5000, ct));   // simulate long work
    return Results.Accepted();
});
```

**When in-process isn't enough:** work is lost if the app restarts, and every instance runs its own copy of timers. For persistent, scheduled, or distributed jobs, use **Hangfire**, **Quartz.NET**, or a message broker (Azure Service Bus, RabbitMQ) with workers. A standalone worker app can be created with `dotnet new worker`.

---

## 22.3 Clean architecture

As an app grows, a single project becomes hard to maintain. **Clean Architecture** (and the related Onion/Hexagonal styles) organizes code in layers where **dependencies point inward** toward the business rules, so the core doesn't depend on frameworks, databases, or the web.

```
┌────────────────────────────────────────────┐
│ Web / API  (Controllers, endpoints, DI setup) │  ← depends on everything below
│ ┌────────────────────────────────────────┐ │
│ │ Infrastructure (EF Core, email, files) │ │  ← implements interfaces
│ │ ┌────────────────────────────────────┐ │ │
│ │ │ Application (use cases, interfaces)│ │ │
│ │ │ ┌────────────────────────────────┐ │ │ │
│ │ │ │ Domain (entities, rules)       │ │ │ │  ← depends on nothing
│ │ │ └────────────────────────────────┘ │ │ │
│ │ └────────────────────────────────────┘ │ │
│ └────────────────────────────────────────┘ │
└────────────────────────────────────────────┘
```

| Layer | Contains | References |
|---|---|---|
| **Domain** | Entities, value objects, domain rules, domain events | Nothing |
| **Application** | Use cases (commands/queries), DTOs, interfaces (`IOrderRepository`, `IEmailSender`), validation | Domain |
| **Infrastructure** | `DbContext`, repositories, email/SMS/payment implementations, file storage | Application, Domain |
| **Web (Presentation)** | Endpoints/controllers, filters, `Program.cs`, DI composition | Application (and Infrastructure only to register it) |

**Typical solution layout:**

```
MyShop.sln
├── src/
│   ├── MyShop.Domain/            (Order.cs, Product.cs, ValueObjects/)
│   ├── MyShop.Application/       (Orders/PlaceOrder.cs, Abstractions/IOrderRepository.cs)
│   ├── MyShop.Infrastructure/    (Persistence/AppDbContext.cs, Email/SmtpEmailSender.cs, DependencyInjection.cs)
│   └── MyShop.Web/               (Endpoints/, Program.cs)
└── tests/
    ├── MyShop.Domain.Tests/
    ├── MyShop.Application.Tests/
    └── MyShop.Web.IntegrationTests/
```

**The dependency rule in practice:** the Application layer defines the interface; Infrastructure implements it; the Web project wires them together.

```csharp
// Application
public interface IOrderRepository { Task AddAsync(Order order, CancellationToken ct); }

public class PlaceOrderHandler
{
    private readonly IOrderRepository _orders;
    public PlaceOrderHandler(IOrderRepository orders) => _orders = orders;

    public async Task<int> HandleAsync(PlaceOrderCommand cmd, CancellationToken ct)
    {
        var order = Order.Create(cmd.CustomerId, cmd.Lines);      // domain rules live in the entity
        await _orders.AddAsync(order, ct);
        return order.Id;
    }
}

// Infrastructure
public class EfOrderRepository : IOrderRepository { /* uses AppDbContext */ }

// Web: Program.cs
builder.Services.AddScoped<IOrderRepository, EfOrderRepository>();
builder.Services.AddScoped<PlaceOrderHandler>();
```

**Related patterns:** CQRS (separate commands and queries), the mediator pattern (the MediatR library; check its current license), the repository and unit-of-work patterns (note that `DbContext` is already a unit of work), domain-driven design (DDD), and **vertical slice architecture** (organizing by feature instead of by layer).

**Be pragmatic.** Clean Architecture adds projects, interfaces, and indirection. For a small CRUD app, a single project with feature folders is simpler and perfectly fine. Introduce layers when complexity, team size, or testability needs justify them.

---

## 22.4 Project ideas

The best way to learn is to build. Here are projects roughly ordered by difficulty, each tied to lessons in this Topic.

| Level | Project | Skills practiced |
|---|---|---|
| Beginner | **Todo API**: CRUD with minimal APIs and in-memory storage | Lessons 1-5 |
| Beginner | **Personal blog**: Razor Pages with posts and categories | Lessons 7-9, 11 |
| Beginner | **URL shortener**: create and redirect short links | Routing, EF Core, caching |
| Intermediate | **Task manager with accounts**: register/login, each user sees only their tasks | Identity, authorization (15-16), EF Core |
| Intermediate | **Product catalog REST API** with pagination, search, DTOs, OpenAPI, versioning | Lessons 11, 18 |
| Intermediate | **Expense tracker** with a Blazor UI and charts | Blazor (10), EF Core, validation |
| Intermediate | **Bookstore** MVC site with shopping cart (sessions) | MVC, sessions, view components |
| Advanced | **JWT-secured API + separate SPA** with refresh tokens, rate limiting, CORS | Lessons 15-17 |
| Advanced | **Real-time chat or live dashboard** | SignalR, authentication, Redis backplane |
| Advanced | **E-commerce backend** with Clean Architecture, background order processing, integration tests | Lessons 12-13, 19, 22 |
| Advanced | **Microservices demo**: two services communicating via gRPC and a message queue, deployed with Docker Compose | gRPC, Docker, health checks |

**Making each project "complete":** write unit and integration tests (Lesson 19); add logging and health checks (Lessons 14, 20); keep secrets out of the repo; containerize it (Lesson 21); set up a CI pipeline; and write a README that explains how to run it.

**Where to learn more:**

- **Official documentation:** *learn.microsoft.com/aspnet/core* (reference, tutorials, and fundamentals).
- **Source code:** the ASP.NET Core repository on GitHub (`dotnet/aspnetcore`) and its release notes for each .NET version.
- **Samples:** `dotnet/AspNetCore.Docs.Samples` and the `eShop` reference application (`dotnet/eShop`).
- **Related topics in this repository:** C# language lessons (the back-link at the top of the introduction), SQL and database fundamentals, Docker and Git under Tools.

**Suggested path:** finish the lessons in order, build two or three of the projects above (one per level), then pick a specialization: **cloud-native** (Azure, containers, Kubernetes), **front-end integration** (Blazor, SPAs), **performance/observability** (OpenTelemetry, load testing), or **security** (identity providers, OAuth/OIDC).

---

[Previous](./[21]-Deployment.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md)
