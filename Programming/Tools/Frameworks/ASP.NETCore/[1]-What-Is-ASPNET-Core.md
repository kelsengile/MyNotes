[Previous](./[0]-Introduction-to-ASP.NETCore.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[2]-Program-And-Hosting.md)

*Basics*

# Lesson 1 - What Is ASP.NET Core

## 1.1 The .NET platform

ASP.NET Core is not a language and not a standalone product. It is a **web framework that sits on top of .NET**, so it helps to know the layers underneath it.

| Layer | What it is | Example |
|---|---|---|
| **Language** | The syntax you write | C# |
| **Compiler** | Turns C# into Intermediate Language (IL) | Roslyn |
| **Runtime** | Executes IL, JIT-compiles it to machine code, manages memory (garbage collector) | CoreCLR |
| **Base Class Library (BCL)** | Core types every app uses | `List<T>`, `Task`, `HttpClient`, `File` |
| **Framework** | Libraries for a kind of app | ASP.NET Core (web), WinForms, MAUI |
| **SDK** | Tools to build, test, and publish | the `dotnet` CLI |

Key facts you need to know:

- **Modern .NET** (formerly ".NET Core") is the cross-platform, open-source successor to **.NET Framework**. ASP.NET Core only runs on modern .NET. Classic "ASP.NET" (Web Forms, old MVC 5) runs only on .NET Framework and is legacy.
- .NET ships a new major version **every November**. Even-numbered versions (8, 10, 12...) are **LTS** (Long Term Support, 3 years). Odd-numbered versions are **STS** (Standard Term Support, shorter). For learning and production, prefer the newest LTS release.
- ASP.NET Core ships as part of the **shared framework** `Microsoft.AspNetCore.App`. You do not install it as a separate package. A web project simply targets it through the `Microsoft.NET.Sdk.Web` SDK.
- Code you write is compiled into a `.dll`. The runtime loads that `.dll` and runs it. Because it is IL, the same build output can run on Windows, Linux, and macOS.

```
Your C# code ──Roslyn──▶ IL (.dll) ──CoreCLR (JIT)──▶ machine code
```

---

## 1.2 Kestrel and cross-platform hosting

Every web app needs a **web server**: a program that listens on a network port, parses HTTP requests, and hands them to your code. ASP.NET Core includes its own server called **Kestrel**.

- Kestrel is written in managed C#, is fast, and runs on every supported OS.
- It supports **HTTP/1.1, HTTP/2, and HTTP/3**, plus HTTPS (TLS).
- It is the server used by default. When you run `dotnet run`, Kestrel starts and listens.

**Kestrel alone or behind a reverse proxy?** Kestrel is safe to expose directly, but production deployments commonly place a **reverse proxy** in front of it:

```
Browser ──▶ Reverse proxy (Nginx / IIS / Azure Front Door / YARP) ──▶ Kestrel ──▶ Your app
```

A reverse proxy can handle TLS certificates, load balancing, request filtering, and serving several apps on one machine.

**Hosting options on Windows:**

| Model | Description |
|---|---|
| **Kestrel (self-hosted)** | Your app is its own process. Default everywhere. |
| **IIS in-process** | The app runs *inside* the IIS worker process (`w3wp.exe`) using an IIS HTTP server. Fastest on IIS. |
| **IIS out-of-process** | IIS forwards requests to a separate Kestrel process. |

Because the server is part of your app (not part of the operating system), you control the hosting model from code, and the same app moves between Windows, Linux, and containers without changes.

**The request journey** (you will build on this in every later lesson):

```
Network request
   └─▶ Kestrel (parses HTTP into an HttpContext)
         └─▶ Middleware pipeline (logging, auth, routing...)
               └─▶ Endpoint (minimal API handler, controller action, Razor Page, Blazor)
                     └─▶ Response travels back through the pipeline to the client
```

---

## 1.3 Installing the SDK and using the dotnet CLI

Install the **.NET SDK** (not just the runtime) from the Download link in the introduction. The SDK includes the runtime, the ASP.NET Core shared framework, and the `dotnet` command.

Verify the installation:

```bash
dotnet --version     # SDK version in use
dotnet --info        # SDK, runtimes, OS details
dotnet --list-sdks   # every installed SDK
```

**Essential commands:**

| Command | Purpose |
|---|---|
| `dotnet new list` | Show available project templates |
| `dotnet new web -n Hello` | Create an empty ASP.NET Core project named `Hello` |
| `dotnet restore` | Download NuGet packages (runs automatically on build/run) |
| `dotnet build` | Compile the project |
| `dotnet run` | Build and start the app |
| `dotnet watch` | Run and automatically rebuild/restart on file changes |
| `dotnet test` | Run unit and integration tests |
| `dotnet publish -c Release` | Produce deployable output |
| `dotnet add package <Name>` | Add a NuGet package |
| `dotnet dev-certs https --trust` | Create and trust a local HTTPS development certificate |

**Common web templates:**

| Template | Short name | Result |
|---|---|---|
| Empty | `web` | Minimal app with one "Hello World" endpoint |
| Web API | `webapi` | Minimal API (or controllers with `--use-controllers`) plus OpenAPI |
| MVC | `mvc` | Controllers + Razor views |
| Razor Pages | `razor` | Page-based web app |
| Blazor Web App | `blazor` | Component-based UI |

Create and run your first app:

```bash
dotnet new web -n Hello
cd Hello
dotnet run
```

The console prints the URLs it listens on (for example `http://localhost:5000`). Open one in a browser and you see `Hello World!`. Press `Ctrl+C` to stop.

---

## 1.4 Tour of a project

After `dotnet new web -n Hello`, the project looks like this:

```
Hello/
├── Properties/
│   └── launchSettings.json
├── appsettings.json
├── appsettings.Development.json
├── Hello.csproj
└── Program.cs
```

(After you build, `bin/` and `obj/` appear. They hold build output and are normally git-ignored. Larger templates also add `wwwroot/`, `Controllers/`, `Views/`, or `Pages/`.)

**`Hello.csproj`**: the project file (MSBuild XML):

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

</Project>
```

- `Sdk="Microsoft.NET.Sdk.Web"` is what makes it a web project (it pulls in ASP.NET Core).
- `TargetFramework` is the .NET version. Use the version you installed (`net8.0`, `net10.0`, ...).
- `Nullable` turns on nullable reference type warnings.
- `ImplicitUsings` adds common `using` statements automatically so you do not write them in every file.
- NuGet packages appear here as `<PackageReference Include="..." Version="..." />`.

**`Program.cs`**: the entry point and the heart of the app:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello World!");

app.Run();
```

It uses **top-level statements**, so there is no `Main` method to write. Lesson 2 explains each line.

**`appsettings.json`**: configuration values (logging levels, connection strings, your own settings). `appsettings.Development.json` overrides values when the environment is `Development`.

**`Properties/launchSettings.json`**: used **only on your development machine** by `dotnet run` and the IDE. It defines launch profiles: ports, the `ASPNETCORE_ENVIRONMENT` value, and whether to open a browser.

```json
{
  "profiles": {
    "http": {
      "commandName": "Project",
      "applicationUrl": "http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

It is **not** deployed with your app, so never rely on it for production settings.

**`wwwroot/`** (when present): the *web root*. Files here (CSS, JavaScript, images) are served as static files by the static file middleware (Lesson 3).

---

[Previous](./[0]-Introduction-to-ASP.NETCore.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[2]-Program-And-Hosting.md)
