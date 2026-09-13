[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)

# NuGet

NuGet is the official package manager for .NET. It installs libraries into a project, tracks them in the project file, and restores them automatically whenever the project is built.

Download [https://learn.microsoft.com/nuget/install-nuget-client-tools](https://learn.microsoft.com/nuget/install-nuget-client-tools)
---

## What Is NuGet?

NuGet packages (`.nupkg` files) are hosted on [nuget.org](https://www.nuget.org), the default public registry. Most .NET developers interact with NuGet through the `dotnet` CLI or their IDE rather than a separate tool.

---

## Core Commands

```bash
dotnet add package <package>        # Add a package to the current project
dotnet remove package <package>     # Remove a package
dotnet restore                      # Download all packages listed in the project file
dotnet list package                 # List installed packages
```

The older, IDE-specific `nuget.exe` CLI also works (`nuget install <package>`), but the `dotnet` CLI is the standard today for cross-platform .NET projects.

---

## Where Dependencies Live

Packages are declared inside the project's `.csproj` file:

```xml
<ItemGroup>
  <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
</ItemGroup>
```

Running `dotnet restore` (or simply `dotnet build`, which restores automatically) downloads everything listed here into a local package cache.

---

## Example Walkthrough

```bash
dotnet new console -o MyApp
cd MyApp
dotnet add package Newtonsoft.Json
dotnet build
```

Creates a new console project, adds the Newtonsoft.Json library, then builds the project, restoring dependencies automatically.

[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)
