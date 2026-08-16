[Previous](./[2]-Running-CSharp-Code.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[4]-Configuration-and-Environment.md)

*Getting Started*

# Lesson 3 - Projects & Solutions (.csproj, .sln, NuGet)

## 3.1 What is a .csproj File?

Every C# project has a `.csproj` file — an XML file describing how the project is built: target framework, dependencies, and output type. You rarely edit it by hand; the `dotnet` CLI manages it for you.

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net8.0</TargetFramework>
  </PropertyGroup>
</Project>
```

---

## 3.2 Solutions (.sln)

A **solution** (`.sln`) groups multiple related projects together — for example, a web API project and its test project. Create one with:

```bash
dotnet new sln -n MySolution
dotnet sln add ./MyApi/MyApi.csproj
```

You don't need a solution for a single small project, but it becomes essential once your app grows into multiple pieces.

---

## 3.3 NuGet Packages

**NuGet** is .NET's package manager, similar to npm for JavaScript or pip for Python. It lets you pull in reusable libraries.

```bash
dotnet add package Newtonsoft.Json
```

This downloads the package and adds a reference to it inside your `.csproj` file automatically.

---

## 3.4 Common `dotnet` CLI Commands

| Command | Purpose |
|---|---|
| `dotnet new console` | Create a new console project |
| `dotnet build` | Compile the project |
| `dotnet run` | Build and run the project |
| `dotnet test` | Run unit tests |
| `dotnet add package <name>` | Install a NuGet package |
| `dotnet restore` | Download all dependencies |

[Previous](./[2]-Running-CSharp-Code.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[4]-Configuration-and-Environment.md)
