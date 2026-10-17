[Previous](./[11]-Entity-Framework-Core.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[13]-Dependency-Injection-And-Services.md)

*Data And Services*

# Lesson 12 - Migrations And Database Management

## 12.1 Creating migrations

When your entity classes change, the database schema must change too. A **migration** is a C# file that describes one schema change (create table, add column, ...) and how to undo it. Migrations give you **version control for your database**.

**Install the EF Core command-line tool once:**

```bash
dotnet tool install --global dotnet-ef
dotnet ef --version
```

The project also needs the `Microsoft.EntityFrameworkCore.Design` package and a provider package (Lesson 11). If your DbContext lives in a different project than the startup project, pass `--project` and `--startup-project`.

**Create the first migration:**

```bash
dotnet ef migrations add InitialCreate
```

This generates a `Migrations/` folder:

```
Migrations/
├── 20260310120000_InitialCreate.cs            ← Up() and Down()
├── 20260310120000_InitialCreate.Designer.cs
└── AppDbContextModelSnapshot.cs               ← the current model state
```

```csharp
public partial class InitialCreate : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "Products",
            columns: table => new
            {
                Id = table.Column<int>(nullable: false)
                    .Annotation("SqlServer:Identity", "1, 1"),
                Name = table.Column<string>(maxLength: 100, nullable: false),
                Price = table.Column<decimal>(precision: 18, scale: 2, nullable: false)
            },
            constraints: table => table.PrimaryKey("PK_Products", x => x.Id));
    }

    protected override void Down(MigrationBuilder migrationBuilder)
        => migrationBuilder.DropTable(name: "Products");
}
```

**How it works.** EF compares your current model to the **snapshot** file and writes the *difference* into a new migration. The snapshot is then updated. **Always read the generated migration** before applying it, as renames can be detected as "drop column + add column", which loses data.

**Add a later migration** after you change an entity:

```bash
dotnet ef migrations add AddProductDescription
```

**Other useful commands:**

| Command | Purpose |
|---|---|
| `dotnet ef migrations list` | Show all migrations and which are applied |
| `dotnet ef migrations remove` | Delete the **last** migration (only if not applied to a database) |
| `dotnet ef migrations script` | Generate a SQL script |
| `dotnet ef dbcontext info` | Show provider and connection info |

**Tips:** use descriptive names (`AddOrderStatus`, not `Update1`); commit migration files to source control; do not edit applied migrations: create a new one instead.

---

## 12.2 Applying and reverting

**Apply all pending migrations** to the database specified in the connection string:

```bash
dotnet ef database update
```

EF creates the database if needed, creates a `__EFMigrationsHistory` table to record applied migrations, and runs each pending migration's `Up()` in order.

**Update to (or revert to) a specific migration:**

```bash
dotnet ef database update AddProductDescription     # go to this migration
dotnet ef database update InitialCreate             # revert later ones (runs their Down())
dotnet ef database update 0                         # revert everything
```

Reverting runs `Down()` methods and can **destroy data** (for example, dropping a column). Never do it casually on production.

**Removing a migration:** if the migration was applied, first revert the database to the previous migration, then run `dotnet ef migrations remove`.

**Generating SQL scripts** (good for DBAs and controlled deployments):

```bash
dotnet ef migrations script --idempotent -o migrate.sql
dotnet ef migrations script InitialCreate AddProductDescription -o changes.sql
```

An **idempotent** script checks `__EFMigrationsHistory` and only applies missing migrations, so it can run safely on any database version.

**Migration bundles** package the migrations as a self-contained executable, good for CI/CD:

```bash
dotnet ef migrations bundle --self-contained -r linux-x64
./efbundle --connection "Host=db;Database=shop;Username=app;Password=..."
```

**Applying migrations at app startup** is convenient for development and small apps:

```csharp
using (var scope = app.Services.CreateScope())
{
    var db = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    await db.Database.MigrateAsync();
}
```

Avoid this in production when you run **multiple instances** (they would race to migrate) or when the app's database account should not have schema-change permissions. Prefer scripts or bundles run as a deployment step.

**Data-loss-safe workflow:**

1. Change the model → add a migration → review the generated code.
2. Run it against a **development** database and test.
3. Generate a script or bundle and run it in staging, then production, with a **backup** taken first.

---

## 12.3 Seeding data

**Seeding** inserts initial data (lookup values, default roles, sample data).

**Option 1: `HasData` (model seeding).** Seed data becomes part of migrations. Keys must be specified explicitly:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Category>().HasData(
        new Category { Id = 1, Name = "Books" },
        new Category { Id = 2, Name = "Games" });
}
```

After adding it, run `dotnet ef migrations add SeedCategories`. Later changes to this data generate `UPDATE`/`DELETE` statements in new migrations. Best for **small, static reference data**. Related entities must use foreign key values (`CategoryId = 1`), not navigation properties.

**Option 2: seeding with code at startup** (flexible; good for data that needs logic, generated values, or is dev-only):

```csharp
public static class DbSeeder
{
    public static async Task SeedAsync(AppDbContext db)
    {
        if (await db.Products.AnyAsync()) return;             // only seed an empty table

        db.Products.AddRange(
            new Product { Name = "Pen",      Price = 1.50m },
            new Product { Name = "Notebook", Price = 4.00m });

        await db.SaveChangesAsync();
    }
}

// Program.cs
using (var scope = app.Services.CreateScope())
{
    var db = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    await db.Database.MigrateAsync();
    if (app.Environment.IsDevelopment())
        await DbSeeder.SeedAsync(db);
}
```

**Option 3: `UseSeeding` / `UseAsyncSeeding` (EF Core 9+).** Seeding logic is registered on the context options and runs as part of `Migrate`/`EnsureCreated`:

```csharp
options.UseSqlServer(connectionString)
       .UseAsyncSeeding(async (context, _, ct) =>
       {
           if (!await context.Set<Category>().AnyAsync(ct))
           {
               context.Set<Category>().Add(new Category { Name = "Books" });
               await context.SaveChangesAsync(ct);
           }
       });
```

**Which to pick?**

| Approach | Good for |
|---|---|
| `HasData` | Small, fixed reference data tracked in migrations |
| Startup code | Development sample data, data requiring logic |
| `UseSeeding` | Idempotent seeding tied to the migration process |

Make seeding **idempotent** (safe to run repeatedly) by checking for existing data.

---

## 12.4 Working with SQL Server and PostgreSQL

EF Core is provider-based. Your entities, LINQ, and migrations workflow stay the same, but the provider package, connection string, and a few behaviors differ.

| | SQL Server | PostgreSQL |
|---|---|---|
| Package | `Microsoft.EntityFrameworkCore.SqlServer` | `Npgsql.EntityFrameworkCore.PostgreSQL` |
| Configure | `options.UseSqlServer(cs)` | `options.UseNpgsql(cs)` |
| Connection string | `Server=localhost;Database=Shop;User Id=sa;Password=...;TrustServerCertificate=True` | `Host=localhost;Port=5432;Database=shop;Username=app;Password=...` |
| Auto-increment key | `IDENTITY` | Identity column / `serial` |
| Unquoted identifier case | Case-insensitive | Folded to lower case (so many teams use snake_case names) |
| String comparison | Collation (usually case-insensitive) | Case-sensitive by default (use `ILike` for case-insensitive search) |
| Time zones | `datetime2`, `datetimeoffset` | `timestamp with time zone` requires UTC `DateTime` values |

**Program.cs examples:**

```csharp
// SQL Server
builder.Services.AddDbContext<AppDbContext>(o =>
    o.UseSqlServer(builder.Configuration.GetConnectionString("Default")));

// PostgreSQL
builder.Services.AddDbContext<AppDbContext>(o =>
    o.UseNpgsql(builder.Configuration.GetConnectionString("Default")));
```

**Run a database locally with Docker:**

```bash
# PostgreSQL
docker run --name pg -e POSTGRES_PASSWORD=secret -e POSTGRES_DB=shop -p 5432:5432 -d postgres:16

# SQL Server
docker run --name mssql -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=Your_strong_Passw0rd" \
  -p 1433:1433 -d mcr.microsoft.com/mssql/server:2022-latest
```

**Provider-specific features** are available via provider packages, for example Npgsql's `EF.Functions.ILike(p.Name, "%pen%")` or PostgreSQL JSONB columns; SQL Server has temporal tables and `EF.Functions.Like`.

**Migrations are provider-specific.** The generated code contains provider annotations (`SqlServer:Identity`, `Npgsql:...`). If you switch providers, **delete and regenerate** the migrations. If you support multiple providers in one codebase, keep separate migration assemblies per provider.

**Connection string safety:** never commit real credentials. Read them from user secrets (development) or environment variables / a secret store (production), as covered in Lesson 14. Enable `EnableRetryOnFailure()` for transient failures in cloud databases:

```csharp
o.UseSqlServer(cs, sql => sql.EnableRetryOnFailure());
o.UseNpgsql(cs, npg => npg.EnableRetryOnFailure());
```

---

[Previous](./[11]-Entity-Framework-Core.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[13]-Dependency-Injection-And-Services.md)
