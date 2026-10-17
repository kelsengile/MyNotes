[Previous](./[10]-Blazor-Components.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[12]-Migrations-And-Database-Management.md)

*Data And Services*

# Lesson 11 - Entity Framework Core

## 11.1 DbContext and entities

**Entity Framework Core (EF Core)** is an **object-relational mapper (ORM)**. It lets you work with a database using C# classes and LINQ instead of writing SQL by hand. EF Core translates your code to SQL, runs it, and turns the results back into objects.

**Install packages** (choose the provider for your database):

```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer      # SQL Server
# dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL      # PostgreSQL
# dotnet add package Microsoft.EntityFrameworkCore.Sqlite       # SQLite
dotnet add package Microsoft.EntityFrameworkCore.Design         # needed for migrations tooling
```

**Entities** are plain C# classes that map to tables. Properties map to columns. A property named `Id` (or `{ClassName}Id`) becomes the **primary key** by convention.

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public decimal Price { get; set; }
    public DateTime CreatedAt { get; set; }
}
```

**The `DbContext`** represents a session with the database. Each `DbSet<T>` property is a table you can query.

```csharp
using Microsoft.EntityFrameworkCore;

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

    public DbSet<Product> Products => Set<Product>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Product>(e =>
        {
            e.ToTable("Products");
            e.Property(p => p.Name).IsRequired().HasMaxLength(100);
            e.Property(p => p.Price).HasPrecision(18, 2);
            e.HasIndex(p => p.Name);
        });
    }
}
```

**Configuring the model:** EF Core builds its model from (1) **conventions**, (2) **data annotations** on entities (`[Required]`, `[MaxLength]`, `[Key]`), and (3) the **Fluent API** in `OnModelCreating`. Fluent API has the highest priority and keeps entities clean.

**Register in `Program.cs`:**

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("Default")));
```

`AddDbContext` registers the context with **Scoped** lifetime: one instance per request, disposed at the end. This matches the DbContext design (a short-lived unit of work). A `DbContext` is **not thread-safe**, so never share one across parallel tasks.

**Use it** by injecting it:

```csharp
app.MapGet("/products", async (AppDbContext db) => await db.Products.ToListAsync());
```

---

## 11.2 LINQ queries

You query with **LINQ**. EF Core translates the expression into SQL when the query is **executed**.

```csharp
// Read
var all        = await db.Products.ToListAsync();
var cheap      = await db.Products.Where(p => p.Price < 20).ToListAsync();
var byId       = await db.Products.FirstOrDefaultAsync(p => p.Id == 5);
var found      = await db.Products.FindAsync(5);                    // by primary key (checks tracked entities first)
var sorted     = await db.Products.OrderBy(p => p.Name).ThenByDescending(p => p.Price).ToListAsync();
var exists     = await db.Products.AnyAsync(p => p.Name == "Pen");
var count      = await db.Products.CountAsync(p => p.Price > 10);
var total      = await db.Products.SumAsync(p => p.Price);
```

**Projection** with `Select` retrieves only the columns you need and shapes results into DTOs:

```csharp
var list = await db.Products
    .Where(p => p.Price > 10)
    .Select(p => new ProductDto(p.Id, p.Name, p.Price))
    .ToListAsync();
```

**Deferred execution.** A query like `db.Products.Where(...)` is only a **description**. SQL runs when you enumerate it (`ToListAsync`, `FirstAsync`, `CountAsync`, `foreach`). That lets you build queries conditionally:

```csharp
IQueryable<Product> query = db.Products;
if (!string.IsNullOrEmpty(search)) query = query.Where(p => p.Name.Contains(search));
if (maxPrice is not null)          query = query.Where(p => p.Price <= maxPrice);
var results = await query.ToListAsync();           // one SQL statement
```

**Writing data:**

```csharp
// Create
var product = new Product { Name = "Pen", Price = 1.5m, CreatedAt = DateTime.UtcNow };
db.Products.Add(product);
await db.SaveChangesAsync();                         // INSERT; product.Id is now populated

// Update
var p = await db.Products.FindAsync(5);
if (p is not null)
{
    p.Price = 2.0m;                                  // change tracked automatically
    await db.SaveChangesAsync();                     // UPDATE
}

// Delete
db.Products.Remove(p);
await db.SaveChangesAsync();                         // DELETE
```

`SaveChangesAsync` sends all pending changes in **one transaction**: either everything succeeds or nothing does.

**Always use the async versions** (`ToListAsync`, `SaveChangesAsync`) in web apps. Be careful: only operations that EF Core can translate to SQL work inside a query. Calling your own C# methods in a `Where` may throw a translation error. Move such logic outside the query or call `AsEnumerable()` deliberately.

**Never build SQL with string concatenation.** If you need raw SQL, use parameterized APIs, where interpolated values become parameters:

```csharp
var results = await db.Products
    .FromSql($"SELECT * FROM Products WHERE Name = {name}")
    .ToListAsync();
```

---

## 11.3 Relationships

Relationships are defined with **navigation properties** and **foreign keys**.

**One-to-many** (one Category has many Products):

```csharp
public class Category
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public List<Product> Products { get; set; } = new();     // collection navigation
}

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = "";

    public int CategoryId { get; set; }                       // foreign key
    public Category Category { get; set; } = null!;           // reference navigation
}
```

EF Core discovers this by convention. To be explicit:

```csharp
modelBuilder.Entity<Product>()
    .HasOne(p => p.Category)
    .WithMany(c => c.Products)
    .HasForeignKey(p => p.CategoryId)
    .OnDelete(DeleteBehavior.Restrict);
```

**One-to-one:**

```csharp
public class User    { public int Id { get; set; } public Profile? Profile { get; set; } }
public class Profile { public int Id { get; set; } public int UserId { get; set; } public User User { get; set; } = null!; }

modelBuilder.Entity<User>().HasOne(u => u.Profile).WithOne(p => p.User)
            .HasForeignKey<Profile>(p => p.UserId);
```

**Many-to-many** (EF Core 5+ creates the join table automatically):

```csharp
public class Student { public int Id { get; set; } public List<Course> Courses { get; set; } = new(); }
public class Course  { public int Id { get; set; } public List<Student> Students { get; set; } = new(); }
```

**Loading related data.** Related entities are **not** loaded unless you ask:

```csharp
// Eager loading: JOIN in the same query
var products = await db.Products.Include(p => p.Category).ToListAsync();

var categories = await db.Categories
    .Include(c => c.Products)
        .ThenInclude(p => p.Reviews)                  // deeper levels
    .ToListAsync();

// Projection is often best: load only what is needed
var dtos = await db.Products
    .Select(p => new { p.Name, Category = p.Category.Name })
    .ToListAsync();

// Explicit loading
await db.Entry(product).Reference(p => p.Category).LoadAsync();
```

`Include` over several collections can produce huge joined result sets; use `.AsSplitQuery()` to run separate queries instead.

**Delete behavior** determines what happens to dependents: `Cascade` (delete children too), `Restrict`/`NoAction` (block deletion), `SetNull`. Required relationships cascade by default.

---

## 11.4 Tracking and performance

**Change tracking.** When you load entities, the `DbContext` keeps a snapshot and tracks them. On `SaveChanges`, it compares current values to the snapshot and generates only the necessary `UPDATE`s.

**Use `AsNoTracking()` for read-only queries.** It skips tracking, reducing memory and CPU:

```csharp
var products = await db.Products.AsNoTracking().ToListAsync();
```

Projections to non-entity types (`Select(p => new Dto(...))`) are never tracked.

**Common performance rules:**

| Problem | Solution |
|---|---|
| **N+1 queries** (one query for the list, then one per row for related data) | Use `Include` or a projection |
| Loading unnecessary columns | Use `Select` to project |
| Returning huge result sets | **Paginate** with `Skip`/`Take` and a stable `OrderBy` |
| Slow filters | Add indexes (`HasIndex`) on columns used in `Where`/`OrderBy` |
| Updating/deleting many rows by loading them first | Use `ExecuteUpdateAsync` / `ExecuteDeleteAsync` |
| Tracking overhead on read-only endpoints | `AsNoTracking()` |

**Pagination:**

```csharp
var page = await db.Products
    .AsNoTracking()
    .OrderBy(p => p.Id)
    .Skip((pageNumber - 1) * pageSize)
    .Take(pageSize)
    .ToListAsync();
```

**Bulk operations without loading entities** (EF Core 7+):

```csharp
await db.Products
    .Where(p => p.CategoryId == 3)
    .ExecuteUpdateAsync(s => s.SetProperty(p => p.Price, p => p.Price * 1.1m));

await db.Products.Where(p => p.Price == 0).ExecuteDeleteAsync();
```

These run immediately as single SQL statements and bypass change tracking.

**Inspect the SQL** while developing:

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(connectionString)
           .LogTo(Console.WriteLine, LogLevel.Information)
           .EnableSensitiveDataLogging(builder.Environment.IsDevelopment()));
```

(`EnableSensitiveDataLogging` shows parameter values: enable it for development only.)

**Concurrency.** To detect two users editing the same row, add a concurrency token (e.g. `[Timestamp] public byte[] RowVersion`). EF then throws `DbUpdateConcurrencyException` if the row changed since it was read.

**Other tips:** keep the `DbContext` lifetime short; use `AddDbContextPool` for high-throughput apps; never keep a context in a static/singleton field.

---

[Previous](./[10]-Blazor-Components.md) | [Table of Contents](./[0]-Introduction-to-ASP.NETCore.md) | [Next](./[12]-Migrations-And-Database-Management.md)
