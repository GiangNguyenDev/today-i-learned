# Create new Web API project

In Visual Studio, add new project with Web API template.

![](images/build-asp-dot-net-core-web-api_1676575868.png)

The project will have a pre defined structure and components, so you can start to build your own project.

![](images/build-asp-dot-net-core-web-api_1676576074.png)

# Add Controller

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

# Add Entity class

```c#
public class Product
{
  public string Name { get; set; }
  public string Description { get; set; }
  public decimal Price { get; set; }
}
```

Entity is a class, which often used to represent a single row of a database table. So, we will fill this Entity class with data, then save them later into database. By reading data from DB, we create instance of the Entity class with data in it.

# Set up Entity Framework