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
  - [Override OnModelCreating method of StoreContext](#override-onmodelcreating-method-of-storecontext)
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
- [Class Diagram](#class-diagram)
  - [Create DTO object](#create-dto-object)
  - [Serving static content from the API](#serving-static-content-from-the-api)
- [API Error Handling](#api-error-handling)
  - [Create ApiResponse class](#create-apiresponse-class)
  - [Configure customer behavior of API](#configure-customer-behavior-of-api)
  - [Error Handling Class Diagram](#error-handling-class-diagram)
- [Add and use Swagger Service](#add-and-use-swagger-service)
- [Sorting, Filtering, Pagination and Searching](#sorting-filtering-pagination-and-searching)
  - [Sorting](#sorting)
- [Filtering](#filtering)
- [Pagination](#pagination)
- [Searching](#searching)
- [Adding CORS Support to API](#adding-cors-support-to-api)

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

## Override OnModelCreating method of StoreContext

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

    // Solution to resolve the issue that SQLite does not support decimal type
    if (Database.ProviderName == "Microsoft.EntityFrameworkCore.Sqlite")
    {
      foreach (var entityType in modelBuilder.Model.GetEntityTypes())
      {
        var properties = entityType.ClrType.GetProperties().Where(p => p.PropertyType == typeof(decimal));

        foreach (var property in properties)
        {
          modelBuilder.Entity(entityType.Name).Property(property.Name).HasConversion<double>();
        }
      }
    }
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

# Class Diagram

```mermaid
classDiagram
direction TB

ProductsController ..> ProductsWithTypesAndBrandsSpecification
ProductsWithTypesAndBrandsSpecification --|> BaseSpecification
BaseSpecification ..|> ISpecification
ProductsController --> IGenericRepository~BaseEntity~
IGenericRepository~BaseEntity~ <|.. GenericRepository
GenericRepository ..> ISpecification
GenericRepository --> StoreContext
StoreContext --|> DbContext
GenericRepository ..> SpecificationEvaluator

class ProductsController {
  +GetProducts()
  +GetProduct(id)
}

class ProductsWithTypesAndBrandsSpecification {
  constructor(productParams)
  constructor(id)
}

class BaseSpecification {
  +Criteria: Expression~Func~T+bool~~
  +Includes: List~Expression~Func~T+object~~~
}

class GenericRepository {
  +CountAsync(spec)
  +GetByIdAsync(id)
  +GetEntityWithSpec(spec)
  +ListAllAsync()
  +ListAsync(spec)
  -ApplySpecification(spec)
}

class SpecificationEvaluator {
  +GetQuery(inputQuery, spec)
}

class StoreContext {
  +Products: DbSet~Product~
  +ProductBrands: DbSet~ProductBrand~
  +ProductTypes: DbSet~ProductType~
}
```

## Create DTO object

DTO stand for Data Transfer Object. It is usually an flatten object, which can be serialized into JSON and transferred via API to the client.

```c#
public class ProductDto {
  public int Id { get; set; }
  public string Name { get; set; }
  public string Description { get; set; }
  public decimal Price { get; set; }
  public string PictureUrl { get; set; }
  public string ProductType { get; set; }
  public string ProductBrand { get; set; }
}
```

```c#
public async Task<ActionResult<ProductToReturnDto>> GetProduct(int id)
{
  var spec = new ProductsWithTypesAndBrandsSpecification(id);
  var product = await _productRepo.GetEntityWithSpec(spec);

  return new ProductToReturnDto()
  {
    Id = product.Id,
    Name = product.Name,
    Description = product.Description,
    PictureUrl = product.PictureUrl,
    Price = product.Price,
    ProductBrand = product.ProductBrand.Name,
    ProductType = product.ProductType.Name,
  };
}
```

## Serving static content from the API

Create a folder `wwwroot` to store the static files like images. As default, the application will search this folder for the static content.

![](images/build-asp-dot-net-core-web-api_1678652861.png)

Enables static file serving for the current request path

```c#
app.UseStaticFiles();
```

# API Error Handling

## Create ApiResponse class

```c#
public class ApiResponse
{
  public ApiResponse(int statusCode, string message = null)
  {
    StatusCode = statusCode;
    Message = message ?? GetDefaultMessageForStatusCode(statusCode);
  }

  public string Message { get; set; }

  public int StatusCode { get; set; }

  private string GetDefaultMessageForStatusCode(int statusCode)
  {
    return statusCode switch
    {
      400 => "A bad request, you have made",
      401 => "Authorized, you are not",
      404 => "Resource found, it was not",
      500 => "Errors are the path to the dark side. Errors lead to anger. Anger leads to hate. Hate leads to career change",
      _ => null,
    };
  }
}
```

## Configure customer behavior of API

```c#
services.Configure<ApiBehaviorOptions>(options =>
{
  options.InvalidModelStateResponseFactory = (actionContext) =>
  {
    var errors = actionContext.ModelState
      .Where(e => e.Value.Errors.Count > 0)
      .SelectMany(x => x.Value.Errors)
      .Select(x => x.ErrorMessage)
      .ToArray();

    var errorResponse = new ApiValidationErrorResponse() { Errors = errors };
    return new BadRequestObjectResult(errorResponse);
  };
});
```

## Error Handling Class Diagram

```mermaid
classDiagram
direction LR

Program ..> ExceptionMiddleware: Register
ExceptionMiddleware ..> ApiException: Create
ApiException --|> ApiResponse

class ApiResponse {
  +Message: string
  +StatusCode: int
}

class ApiException {
  +Details: string
}
```

# Add and use Swagger Service

```c#
services.AddSwaggerGen(c =>
{
  c.SwaggerDoc("v1", new OpenApiInfo { Title = "SkiNet API", Version = "v1" });
});

// ...

app.UseSwagger();
app.UseSwaggerUI(c => c.SwaggerEndpoint("/swagger/v1/swagger.json", "SkiNet API v1"));
```

# Sorting, Filtering, Pagination and Searching

## Sorting

```c#
public interface ISpecification<T>
{
  Expression<Func<T, object>> OrderBy { get; }

  Expression<Func<T, object>> OrderByDescending { get; }
}
```

```c#
public class BaseSpecification<T> : ISpecification<T>
{
  public Expression<Func<T, object>> OrderBy { get; private set; }

  public Expression<Func<T, object>> OrderByDescending { get; private set; }

  protected void AddOrderBy(Expression<Func<T, object>> orderByExpression)
  {
    OrderBy = orderByExpression;
  }

  protected void AddOrderByDescending(Expression<Func<T, object>> orderByDescExpression)
  {
    OrderByDescending = orderByDescExpression;
  }
}
```

```c#
public class SpecificationEvaluator<TEntity> where TEntity : BaseEntity
{
  public static IQueryable<TEntity> GetQuery(IQueryable<TEntity> inputQuery, ISpecification<TEntity> spec)
  {
    var query = inputQuery;

    //...

    if (spec.OrderBy != null)
      query = query.OrderBy(spec.OrderBy);

    if (spec.OrderByDescending != null)
      query = query.OrderByDescending(spec.OrderByDescending);

    query = spec.Includes.Aggregate(query, (current, include) => current.Include(include));
    return query;
  }
}
```

```c#
public class ProductsWithTypesAndBrandsSpecification : BaseSpecification<Product>
{
  public ProductsWithTypesAndBrandsSpecification(string sort)
  {
    AddOrderBy(x => x.Name);

    if (!string.IsNullOrEmpty(sort))
    {
      switch (sort)
      {
        case "priceAsc":
            AddOrderBy(x => x.Price);
            break;
        case "priceDesc":
            AddOrderByDescending(x => x.Price);
            break;
        default:
            AddOrderBy(x => x.Name);
            break;
      }
    }
  }
}
```

```c#
public class ProductsController : BaseApiController
{
  [HttpGet]
  public async Task<ActionResult<IReadOnlyList<ProductToReturnDto>>> GetProducts(string sort)
  {
      var spec = new ProductsWithTypesAndBrandsSpecification(sort);
      var products = await productRepo.ListAsync(spec);
      return Ok(mapper.Map<IReadOnlyList<Product>, IReadOnlyList<ProductToReturnDto>>(products));
  }
}
```

# Filtering

```c#
public class ProductsWithTypesAndBrandsSpecification : BaseSpecification<Product>
{
  public ProductsWithTypesAndBrandsSpecification(string sort, int? brandId, int? typeId)
    : base(x =>
      (!brandId.HasValue || x.ProductBrandId == brandId)
      && (!typeId.HasValue || x.ProductTypeId == typeId)
    )
  {
    //...
  }
}
```

# Pagination

```c#
public interface ISpecification<T>
{
  //...

  int Take { get; }

  int Skip { get; }

  bool IsPagingEnabled { get; }
}
```

```c#
public class BaseSpecification<T> : ISpecification<T>
{
  //...

  public int Take { get; private set; }

  public int Skip { get; private set; }

  public bool IsPagingEnabled { get; private set; }

  protected void ApplyPaging(int skip, int take)
  {
    Skip = skip;
    Take = take;
    IsPagingEnabled = true;
  }
}
```

```c#
public class ProductSpecParams
{
  private const int MaxPageSize = 50;

  private int _pageSize = 6;

  public int? BrandId { get; set; }

  public int PageIndex { get; set; } = 1;

  public int PageSize
  {
    get => _pageSize;
    set => _pageSize = (value > MaxPageSize) ? MaxPageSize : value;
  }

  public string Sort { get; set; }

  public int? TypeId { get; set; }
}
```

`FromQuery` attribute specifies that a parameter or property should be bound using the request query string

```c#
public class ProductsWithTypesAndBrandsSpecification : BaseSpecification<Product>
{
  public ProductsWithTypesAndBrandsSpecification(ProductSpecParams productParams)
    : base(x =>
      (!productParams.BrandId.HasValue || x.ProductBrandId == productParams.BrandId)
      && (!productParams.TypeId.HasValue || x.ProductTypeId == productParams.TypeId)
    )
  {
    //...

    ApplyPaging(productParams.PageSize * (productParams.PageIndex - 1), productParams.PageSize);

    //...
  }
}
```

```c#
public class Pagination<T> where T : class
{
  public Pagination(int pageIndex, int pageSize, int count, IReadOnlyList<T> data)
  {
    PageIndex = pageIndex;
    PageSize = pageSize;
    Count = count;
    Data = data;
  }

  public int PageIndex { get; set; }

  public int PageSize { get; set; }

  public int Count { get; set; }

  public IReadOnlyList<T> Data { get; set; }
}
```

```c#
public interface IGenericRepository<T> where T : BaseEntity
{
  Task<int> CountAsync(ISpecification<T> spec);
}
```

```c#
public class GenericRepository<T> : IGenericRepository<T> where T : BaseEntity
{
  public async Task<int> CountAsync(ISpecification<T> spec)
  {
    return await ApplySpecification(spec).CountAsync();
  }
}
```

```c#
public class SpecificationEvaluator<TEntity> where TEntity : BaseEntity
{
  public static IQueryable<TEntity> GetQuery(IQueryable<TEntity> inputQuery, ISpecification<TEntity> spec)
  {
    var query = inputQuery;

    //...

    // The paging must come after other filtering and ordering
    if (spec.IsPagingEnabled)
      query = query.Skip(spec.Skip).Take(spec.Take);

    query = spec.Includes.Aggregate(query, (current, include) => current.Include(include));
    return query;
  }
}
```

```c#
public class ProductWithFiltersForCountSpecification : BaseSpecification<Product>
{
  public ProductWithFiltersForCountSpecification(ProductSpecParams productParams)
    : base(x =>
      (!productParams.BrandId.HasValue || x.ProductBrandId == productParams.BrandId)
      && (!productParams.TypeId.HasValue || x.ProductTypeId == productParams.TypeId)
    )
  {
  }
}
```

```c#
public class ProductsController : BaseApiController
{
  //...

  [HttpGet]
  public async Task<ActionResult<Pagination<ProductToReturnDto>>> GetProducts(
    [FromQuery] ProductSpecParams productParams)
  {
    var countSpec = new ProductWithFiltersForCountSpecification(productParams);
    var totalItems = await productRepo.CountAsync(countSpec);
    var spec = new ProductsWithTypesAndBrandsSpecification(productParams);
    var products = await productRepo.ListAsync(spec);
    var data = mapper.Map<IReadOnlyList<Product>, IReadOnlyList<ProductToReturnDto>>(products);
    return Ok(new Pagination<ProductToReturnDto>(productParams.PageIndex, productParams.PageSize, totalItems, data));
  }
}
```

# Searching

```c#
public class ProductSpecParams
{
  private string _search;

  public string Search
  {
    get => _search;
    set => _search = value.ToLower();
  }
}
```

```c#
public class ProductsWithTypesAndBrandsSpecification : BaseSpecification<Product>
{
  public ProductsWithTypesAndBrandsSpecification(ProductSpecParams productParams)
    : base(x =>
      (string.IsNullOrEmpty(productParams.Search) || x.Name.ToLower().Contains(productParams.Search))
      && (!productParams.BrandId.HasValue || x.ProductBrandId == productParams.BrandId)
      && (!productParams.TypeId.HasValue || x.ProductTypeId == productParams.TypeId)
    )
  {
    //...
  }
}
```

```c#
public class ProductWithFiltersForCountSpecification : BaseSpecification<Product>
{
  public ProductWithFiltersForCountSpecification(ProductSpecParams productParams)
    : base(x =>
      (!productParams.BrandId.HasValue || x.ProductBrandId == productParams.BrandId)
      && (!productParams.TypeId.HasValue || x.ProductTypeId == productParams.TypeId)
    )
  {
  }
}
```

# Adding CORS Support to API

Enable CORS policy to share resources with client side on another domain

```c#
public static class ApplicationServicesExtensions
{
  public static IServiceCollection AddApplicationServices(this IServiceCollection services, IConfiguration config)
  {
    //...

    services.AddCors(opt =>
    {
      opt.AddPolicy("CorsPolicy", policy =>
      {
        policy.AllowAnyHeader().AllowAnyMethod().WithOrigins("https://localhost:4200");
      });
    });

    return services;
  }
}
```

In `Program.cs`, add middleware for CORS policy to add Access-Control-Allow-Origin into header response

```c#
app.UseCors("CorsPolicy");
```

Response Headers:

![](images/build-asp-dot-net-core-web-api_1680969725.png)