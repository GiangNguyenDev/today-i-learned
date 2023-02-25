- [Create new Web API project](#create-new-web-api-project)
- [Add Controller into API](#add-controller-into-api)
- [Create Entity class](#create-entity-class)
- [Set up Entity Framework](#set-up-entity-framework)
- [Create StoreContext](#create-storecontext)
- [Set up Connection String for database](#set-up-connection-string-for-database)
  - [Register DbContext service](#register-dbcontext-service)
- [Install Entity Framework Core .NET CLI tools](#install-entity-framework-core-net-cli-tools)
- [Work with Database and Migrations](#work-with-database-and-migrations)
  - [Create migration](#create-migration)
  - [Update database](#update-database)
  - [Drop database](#drop-database)
  - [Remove migration](#remove-migration)
- [Implement a method of Controller](#implement-a-method-of-controller)
- [Project Structure](#project-structure)
- [Repository Pattern](#repository-pattern)
  - [Create Repository](#create-repository)
- [Configuring the migrations](#configuring-the-migrations)
  - [Defining a configuration class](#defining-a-configuration-class)
  - [Applying the configuration to StoreContext](#applying-the-configuration-to-storecontext)
  - [Apply the migration before running the application](#apply-the-migration-before-running-the-application)
  - [Add Seed data](#add-seed-data)

# Create new Web API project

In Visual Studio, add new project with Web API template.

![](images/build-asp-dot-net-core-web-api_1676575868.png)

The project will have a pre defined structure and components to help you start to build your own project.

![](images/build-asp-dot-net-core-web-api_1676576074.png)

# Add Controller into API

```cs
[ApiController]
[Route("api/[controller]")]
public class WeatherForecastController : ControllerBase
{
  [HttpGet(Name = "GetWeatherForecast")]
  public IEnumerable<WeatherForecast> Get()
  {
    //...
  }
}
```

- `ApiController` indicates that `WeatherForecastController` and all derived types are used to serve HTTP API responses.
- `Route` specifies an attribute route on the controller.
- All controller type must derive from `ControllerBase`, an base class for an MVC controller without view support.
- `HttpGet` identifies an action that supports the HTTP GET method. If the method should have a custom name, please use property `Name` of the `HttpGet` attribute.

# Create Entity class

Entity is a class, which often used to represent a single row of a database table. So, we will fill this Entity class with data, then save them later into database. By reading data from DB, we create instance of the Entity class with data in it.

If in an entity class, we create two properties `ProductType` and `ProductTypeId`, the naming convention helps entity framework to recognize that the `ProductTypeId` is the foreign key of the other property `ProductType`.

```cs
public class Product : BaseEntity
{
  public string Name { get; set; }
  public string Description { get; set; }
  public decimal Price { get; set; }
  public string PictureUrl { get; set; }
  public ProductType ProductType { get; set; }
  public int ProductTypeId { get; set; }
  public ProductBrand ProductBrand { get; set; }
  public int ProductBrandId { get; set; }
}
```

# Set up Entity Framework

Install Nuget packages

- `Microsoft.EntityFrameworkCore.Sqlite`
- `Microsoft.EntityFrameworkCore.Design`

with version compatible to .NET in your project.

# Create StoreContext

```cs
public class StoreContext : DbContext
{
  public StoreContext(DbContextOptions<StoreContext> options) : base(options)
  {
  }

  public DbSet<Product> Products { get; set; }

  public DbSet<ProductType> ProductTypes { get; set; }

  public DbSet<ProductBrand> ProductBrands { get; set; }

  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
    base.OnModelCreating(modelBuilder);
    modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
    // ...
  }
}
```

- `StoreContext` is a `DbContext` instance, which represents a session with the database and can be used to query and save instances of your entities. `DbContext` is a combination of the Unit Of Work and Repository patterns. It is easier with these patterns to write unit test and mock data later.
- `DbContextOptions` are the options to be used in `DbContext`.
- Each property in the `StoreContext` represent a database table.

# Set up Connection String for database

Connection String configures, how to connect with the database. The authentication can also be set up here if needed. You can define the Connection String in `appsettings.json` or `appsettings.Development.json` file.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Data source=mydatabase.db"
  }
}
```

- `DefaultConnection`: Name of Connection String
- `Data source`: Syntax for Sqlite data source
- `mydatabase.db`: Name of database

## Register DbContext service

In the main file `program.cs`, register the `DbContext` as a service, so it can be used later in the application.

```cs
builder.Services.AddDbContext<StoreContext>(opt => opt.UseSqlite(builder.Configuration.GetConnectionString("DefaultConnection")));
```

# Install Entity Framework Core .NET CLI tools

These tools help you to manage Migrations and to scaffold a DbContext and entity types by reverse engineering the schema of a database.

_Source: https://learn.microsoft.com/en-us/ef/core/cli/dotnet_

# Work with Database and Migrations

A schema migration or database migration refers to the management of version-controlled, incremental and reversible changes to relational database schemas.

## Create migration

```console
dotnet ef migrations add <MigrationName> -p <ProjectName> -o <OutputDirectory> -s <StartupProjectName>
```

## Update database

```console
dotnet ef database update
```

## Drop database

```console
dotnet ef database drop -p <ProjectName> -s <StartupProjectName>
```

## Remove migration

```console
dotnet ef migrations remove -p <ProjectName> -s <StartupProject>
```

# Implement a method of Controller

Your Controller will provide endpoints and their functionalities for the API. Each endpoint is represented with a public method. E.g.:

```cs
[HttpGet("{id}")]
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(typeof(ApiResponse), StatusCodes.Status404NotFound)]
public async Task<ActionResult<Product>> GetProduct(int id)
{
  //...
}
```

- `HttpGet`: Identifies an action that supports the HTTP GET method.
- `ProducesResponseType`: A filter that specifies the type of the value and status code returned by the action.
- `ActionResult<T>`: Enables returning a type deriving from `ActionResult` (e.g., `Ok(Product)`, `NotFound(new ApiResponse(404))`) or return a specific type T (e.g., `Product`).

# Project Structure

![](images/build-asp-dot-net-core-web-api_1676838645.png)

- API: Startup project, which contains dependency injection container and middle wares. It is responsible for routing incoming requests to Controllers.
- Infrastructure: Responsible for sending data to database
- Core: Used by other projects and does not depend on any other projects.

# Repository Pattern

Some of benefits when using Repository Pattern in .NET project:

- Decouple business code from data access
- Separation of concerns
- Minimize duplicate query logic
- Testability

![](images/build-asp-dot-net-core-web-api_1676838671.png)

## Create Repository

```cs
public interface IProductRepository
{
  Task<Product> GetProductByIdAsync(int id);
  Task<IReadOnlyList<Product>> GetProductsAsync();
}
```

```cs
public class ProductRepository : IProductRepository
{
  private readonly StoreContext _context;

  public ProductRepository(StoreContext context)
  {
    _context = context;
  }

  public async Task<Product> GetProductByIdAsync(int id)
  {
    //...
  }

  public async Task<IReadOnlyList<Product>> GetProductsAsync()
  {
    //...
  }
}
```

# Configuring the migrations

## Defining a configuration class

```cs
public class ProductConfiguration : IEntityTypeConfiguration<Product>
{
  public void Configure(EntityTypeBuilder<Product> builder)
  {
    builder.Property(p => p.Id).IsRequired();
    builder.Property(p => p.Name).IsRequired().HasMaxLength(100);
    builder.Property(p => p.Description).IsRequired().HasMaxLength(180);
    builder.Property(p => p.Price).HasColumnType("decimal(18, 2)");
    builder.Property(p => p.PictureUrl).IsRequired();
    builder.HasOne(p => p.ProductBrand).WithMany().HasForeignKey(p => p.ProductBrandId);
    builder.HasOne(p => p.ProductType).WithMany().HasForeignKey(p => p.ProductTypeId);
  }
}
```

- `builder.HasOne(p => p ProductBrand)`: Each `Product` has one `ProductBrand`
- `.WithMany()`: Each `ProductBrand` can be associated with many `Product`s

## Applying the configuration to StoreContext

```cs
public class StoreContext : DbContext
{
  // ...

  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
    base.OnModelCreating(modelBuilder);

    // Applies configuration from all IEntityTypeConfiguration<Product> instances
    // that are defined in provided assembly
    modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly());
  }
}
```

## Apply the migration before running the application

```cs
using var scope = app.Services.CreateScope();
var services = scope.ServiceProvider;
var context = services.GetRequiredService<StoreContext>();
var logger = services.GetRequiredService<ILogger<Program>>();

try
{
	await context.Database.MigrateAsync();
}
catch (Exception ex)
{
	logger.LogError(ex, "An error occurred during migration");
}

app.Run();
```

## Add Seed data

```cs
public class StoreContextSeed
{
  public static async Task SeedAsync(StoreContext context)
  {
    if (!context.ProductBrands.Any())
    {
      var brandsData = File.ReadAllText("../Infrastructure/Data/SeedData/brands.json");
      var brands = JsonSerializer.Deserialize<List<ProductBrand>>(brandsData);
      context.ProductBrands.AddRange(brands);
    }

    if (!context.ProductTypes.Any())
    {
      var typesData = File.ReadAllText("../Infrastructure/Data/SeedData/types.json");
      var types = JsonSerializer.Deserialize<List<ProductType>>(typesData);
      context.ProductTypes.AddRange(types);
    }

    if (!context.Products.Any())
    {
      var productsData = File.ReadAllText("../Infrastructure/Data/SeedData/products.json");
      var products = JsonSerializer.Deserialize<List<Product>>(productsData);
      context.Products.AddRange(products);
    }

    if (context.ChangeTracker.HasChanges()) await context.SaveChangesAsync();
  }
}
```
