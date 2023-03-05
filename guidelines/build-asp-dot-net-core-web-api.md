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
- [Generic Repository](#generic-repository)
  - [Benefits when using Generic Repository Pattern](#benefits-when-using-generic-repository-pattern)
  - [Introduce to Specification Pattern](#introduce-to-specification-pattern)
  - [Create specification interface](#create-specification-interface)
  - [Create base class of specification](#create-base-class-of-specification)
  - [Implement SpecificationEvaluator](#implement-specificationevaluator)
  - [Implement the repository with specification methods](#implement-the-repository-with-specification-methods)
  - [Create specification class](#create-specification-class)
  - [Use specification in Controller class](#use-specification-in-controller-class)

# Create new Web API project

In Visual Studio, add new project with Web API template.

![](images/build-asp-dot-net-core-web-api_1676575868.png)

The project will have a pre defined structure and components to help you start to build your own project.

![](images/build-asp-dot-net-core-web-api_1676576074.png)

# Add Controller into API

```c#
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

```c#
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

```c#
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

```c#
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

```c#
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

```c#
public interface IProductRepository
{
  Task<Product> GetProductByIdAsync(int id);
  Task<IReadOnlyList<Product>> GetProductsAsync();
}
```

```c#
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

```c#
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

```c#
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

```c#
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

```c#
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

# Generic Repository

## Benefits when using Generic Repository Pattern

- Single and simple repository for many entities
- Reduce data access

```c#
public interface IGenericRepository<T> where T : BaseEntity
{
  Task<T> GetByIdAsync(int id);

  Task<IReadOnlyList<T>> ListAllAsync();

  Task<T> GetEntityWithSpec(ISpecification<T> spec);

  Task<IReadOnlyList<T>> ListAsync(ISpecification<T> spec);

  Task<int> CountAsync(ISpecification<T> spec);
}
```

```c#
public class GenericRepository<T> : IGenericRepository<T> where T : BaseEntity
{
  private readonly StoreContext _context;

  public GenericRepository(StoreContext context)
  {
    _context = context;
  }

  public async Task<int> CountAsync(ISpecification<T> spec)
  {
    // ...
  }

  public async Task<T> GetByIdAsync(int id)
  {
    return _context.Set<T>().FindAsync(id);
  }

  public async Task<T> GetEntityWithSpec(ISpecification<T> spec)
  {
    this. _context.Set<T>().ToListAsync();
  }

  public async Task<IReadOnlyList<T>> ListAllAsync()
  {
    // ...
  }

  public async Task<IReadOnlyList<T>> ListAsync(ISpecification<T> spec)
  {
    // ...
  }
}
```

After creating the Generic Repository, we can add it as a scoped service. Because we don't know the type of our Generic Repository at the beginning (IGenericRepository<Product> or IGenericRepository<ProductBrand>), we must use the keyword `typeof` here.

```c#
builder.Services.AddScoped(typeof(IGenericRepository<>), typeof(GenericRepository<>));
```

## Introduce to Specification Pattern

Unfortunately Generic Repository has sometimes bad reputation.

- It is difficult to implement data access with specific requirement
- Paginating, filtering or sorting of data.
- Leaky abstraction
- Not meaningful method name

To overcome all of this, we will apply specification pattern.

- Describes a query in an object
- Return an IQueryable<T>
- Generic List method takes specification as parameter
- Specification can have meaningful name

## Create specification interface

```c#
public interface ISpecification<T>
{
  /// <summary>
  /// Store filtering expression
  /// </summary>
  Expression<Func<T, bool>> Criteria { get; }

  /// <summary>
  /// Store expression, that specifies the related entities to include in the query result
  /// </summary>
  List<Expression<Func<T, object>>> Includes { get; }
}
```

## Create base class of specification

```c#
public class BaseSpecification<T> : ISpecification<T>
{
  public BaseSpecification() {}

  public BaseSpecification(Expression<Func<T, bool>> criteria)
  {
    Criteria = criteria;
  }

  public Expression<Func<T, bool>> Criteria { get; }

  public List<Expression<Func<T, object>>> Includes { get; } = new List<Expression<Func<T, object>>>();

  /// <summary>
  /// Include entity of other database table into the current table
  /// </summary>
  /// <param name="includeExpression"></param>
  protected void AddInclude(Expression<Func<T, object>> includeExpression)
  {
    Includes.Add(includeExpression);
  }
}
```

## Implement SpecificationEvaluator

```c#
public class SpecificationEvaluator<TEntity> where TEntity : BaseEntity
{
  /// <summary>
  /// Applies specification expressions (filtering, sorting, includes) to the input query
  /// </summary>
  public static IQueryable<TEntity> GetQuery(IQueryable<TEntity> inputQuery, ISpecification<TEntity> spec)
  {
    var query = inputQuery;

    if (spec.Criteria != null)
      query = query.Where(spec.Criteria);

    query = spec.Includes.Aggregate(query, (current, include) => current.Include(include));
    return query;
  }
}
```

## Implement the repository with specification methods

```c#
public class GenericRepository<T> : IGenericRepository<T> where T : BaseEntity
{
  public async Task<T> GetEntityWithSpec(ISpecification<T> spec)
  {
    return await ApplySpecification(spec).FirstOrDefaultAsync();
  }

  public async Task<IReadOnlyList<T>> ListAsync(ISpecification<T> spec)
  {
    return await ApplySpecification(spec).ToListAsync();
  }

  /// <summary>
  /// Apply specification for a generic DbSet of current context
  /// </summary>
  /// <param name="spec">Specification</param>
  /// <returns>Filtered query</returns>
  private IQueryable<T> ApplySpecification(ISpecification<T> spec)
  {
    return SpecificationEvaluator<T>.GetQuery(_context.Set<T>().AsQueryable(), spec);
  }
}
```

## Create specification class

```c#
public class ProductsWithTypesAndBrandsSpecification : BaseSpecification<Product>
{
  public ProductsWithTypesAndBrandsSpecification()
  {
    AddInclude(x => x.ProductType);
    AddInclude(x => x.ProductBrand);
  }
}
```

## Use specification in Controller class

```c#
public class ProductsController : BaseApiController
{
  [HttpGet]
  public async Task<ActionResult<List<Product>>> GetProducts()
  {
    var spec = new ProductsWithTypesAndBrandsSpecification();
    var products = await _productRepo.ListAsync(spec);
    return Ok(products);
  }
}
```
