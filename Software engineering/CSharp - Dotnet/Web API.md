A Web API is an application that allows other programs to communicate with our application over HTTP. For example, imagine we are building a backend (BE) for a shopping application. A frontend (FE) might send `GET /api/products`. The API receives the request, finds the products, and sends back JSON
```json
[
    {
        "id": 1,
        "name": "Laptop",
        "price": 1200
    },
    {
        "id": 2,
        "name": "Mouse",
        "price": 30
    }
]
```

The FE does not need to know how the data was retrieved. It only needs to know how to communicate with the API.

In .NET, we can build web APIs using ASP.NET Core. There are two major approaches: **Minimal APIs** and **controller-based APIs**. Here we will use the **controller-based approach**

# 1. Let's build

We will build a simple Product API. The final API will have endpoints such as:
```text
GET    /api/products
GET    /api/products/1
POST   /api/products
PUT    /api/products/1
DELETE /api/products/1
```

These correspond roughly to CRUD operations:
```text
GET     -> read data
POST    -> create data
PUT     -> update data
DELETE  -> delete data
```

Understand the architecture we will gradually build:
```text
Client
 HTTP request
  v
ASP.NET Core
  v
Controller
  v
Service
  v
Database (DB)
```

The response travels back in the opposite direction:
```text
 DB
  v
Service
  v
Controller
  v
ASP.NET Core
  v
Client
```

# 2. Create the project

We'll use the dotnet CLI to create the project:

```bash
dotnet new webapi --use-controllers -o ProductApi
cd ProductApi
```

`--use-controllers` tells the template to create a controller-based API rather than a Minimal API. We can then run the application using `dotnet run`. It will start a web server, and we get an address like `https://localhost:7000` The port can differ.

# 3. Understand the project structure

As our app becomes larger, we will organise it more clearly:
```text
ProductApi/
│
├── Controllers/
│   └── ProductsController.cs
│
├── Models/
│   └── Product.cs
│
├── DTOs/
│   └── ProductDto.cs
│
├── Services/
│   ├── IProductService.cs
│   └── ProductService.cs
│
├── Data/
│   └── AppDbContext.cs
│
├── Properties/
│   └── launchSettings.json
│
├── appsettings.json
├── appsettings.Development.json
├── Program.cs
└── ProductApi.csproj
```

Each part has a purpose:
- **Controllers** -> receive HTTP requests and return HTTP responses
- **Models** -> represent application/domain data
- **DTOs** -> data transfer objectc represent data that enters/leaves the API
- **Services** -> contain application/business logic
- **Data** -> database-related code
- **Program.cs** -> configures and starts the application
- **appsettings.json** -> application configuration

Think of the project like a restaurant:
- **Controller** = waiter
- **Service** = kitchen
- **DB** = storage room
- **Model** = representation of the food/data
- **Program.cs** = restaurant setup

# 4. `Program.cs`

A basic controller-based app looks like this:
```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();

var app = builder.Build();

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

`Program.cs` is responsible for configuring and starting the app:
```text
builder
  configure services
   v
builder.Build()
  create app
   v
  app
  config middleware/endpoints
   v
app.Run()
```

# 5. `builder`

`var builder = WebApplication.CreateBuilder(args);` creates the application builder. The builder is where we configure things the app needs. 

`builder.Services.AddControllers();` adds the services required for controller-based APIs to the app's dependency injection container.

We will later add things such as:
```csharp
builder.Services.AddDbContext<AppDbContext>();
builder.Services.AddScoped<IProductService, ProductService>();
builder.Services.AddCors(...);
```
```text
builder.Services
    
      +-- Controllers
      +-- Database
      +-- Services
      +-- CORS
      +-- Authentication
      +-- Other dependencies
```

# 6. Dependency injection

ASP.NET Core has a built-in **dependency injection (DI) container**. Instead of a controller manually creating everything it needs:
```csharp
var service = new ProductService();
```

we can do:
```csharp
builder.Services.AddScoped<IProductService, ProductService>();
```

This means when something asks for `IProductService`, provide a `ProductService`. Then the controller can simply receive it:
```csharp
public ProductsController(IProductService productService)
{
    _productService = productService;
}
```

The framework creates the controller and supplies the dependency.

# 7. Let's create a model

```csharp
// dir: Models/Product.cs
namespace ProductApi.Models;

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public decimal Price { get; set; }
}
```

The C# object and json representation correspond to each other:
```json
{
    "id": 1,
    "name": "Laptop",
    "price": 1200
}
```

# 8. Let's write a controller

```csharp
// dir: Controllers/ProductsController.cs
using Microsoft.AspNetCore.Mvc;
using ProductApi.Models;

namespace ProductApi.Controllers;

[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase { }
```

- `[ApiController]` tells ASP.NET Core that this is an API controller and enables API-specific behavior such as automatic model validation responses and parameter binding conventions. 
- `[Route("api/[controller]")]` defines the base URL for the controller. Here `[controller]` takes the name of the controller and becomes `products`. This is called **attribute routing**.
- `ControllerBase` provides useful methods for returning HTTP responses, such as: `Ok()`, `NotFound()`, `BadRequest()`, `CreatedAtAction()`. For API controllers, `ControllerBase` is preferred over `Controller` because `Controller` also contains functionality for MVC views

# 9. GET

```csharp
// dir: Controllers/ProductsController.cs | inside the controller
[HttpGet]
public ActionResult<IEnumerable<Product>> GetProducts()
{
    var products = new List<Product>
    {
        new Product { Id = 1, Name = "Laptop", Price = 1200 },
        new Product { Id = 2, Name = "Mouse", Price = 30 }
    };

    return Ok(products);
}
```

- `[HttpGet]` tells ASP.NET Core to handle HTTP GET requests matching this route.
- `GetProducts()` contains the code that runs when the endpoint is called.

The flow is: `GET /api/products` -> `ProductsController` -> `GetProducts()` -> `products` -> `HTTP 200 + JSON`

The client receives:

```json
[
    {
        "id": 1,
        "name": "Laptop",
        "price": 1200
    },
    {
        "id": 2,
        "name": "Mouse",
        "price": 30
    }
]
```

An API doesn't just return data, it also returns an HTTP status code. Some important ones are:
```text
200 OK
201 Created
204 No Content
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
500 Internal Server Error
```

For example `return Ok(products)` returns `200 OK`, `return NotFound()` returns `404 Not Found`. Think of the it as a short summary attached to the response:
```text
HTTP response
 +-- status code
 +-- headers
 +-- body
```

Now we want `GET /api/products/1`:
```csharp
[HttpGet("{id}")]
public ActionResult<Product> GetProduct(int id) =>
	Ok(new Product { Id = id, Name = "Laptop", Price = 1200 });
```

Suppose the client sends `GET /api/products/42`, ASP.NET Core sees that `{id}` from the route corresponds to the `id` parameter. This process is called **model binding**.

# 10. POST
```csharp
[HttpPost]
public ActionResult<Product> CreateProduct(Product product)
{
    product.Id = 3;

    return CreatedAtAction(
        nameof(GetProduct),
        new { id = product.Id },
        product
    );
}
```

The client sends:
```http
POST /api/products
Content-Type: application/json
```

with:
```json
{
    "name": "Keyboard",
    "price": 80
}
```

ASP.NET Core converts the json request body into `Product product`, then our method executes. The response is `201 Created` with the created product. The important idea is: **JSON request -> Model binding -> Product object -> Controller -> HTTP response**

But why `CreatedAtAction`? and not `Ok(product)`. Because a successful creation is conventionally represented by `201 Created`. `CreatedAtAction` can return the created resource while also indicating the URL where that resource can be retrieved.
```text
POST /api/products
        v
201 Created
        +-- Location: /api/products/3
        +-- body: created product
```
# 11. PUT

```csharp
[HttpPut("{id}")]
public IActionResult UpdateProduct(int id, Product product)
{
    if (id != product.Id) return BadRequest();

    // Update product in db

    return NoContent();
}
```

The request might look like:
```http
PUT /api/products/1
Content-Type: application/json
```

with:
```json
{
    "id": 1,
    "name": "Gaming Laptop",
    "price": 1500
}
```

# 12. DELETE

```csharp
[HttpDelete("{id}")]
public IActionResult DeleteProduct(int id)
{
    // Delete product from database.

    return NoContent();
}
```

The request:
```text
DELETE /api/products/1
```

causes:
```text
ProductsController
       v
DeleteProduct(1)
       v
Delete from db
       v
204 No Content
```

# 13. Checkpoint

We now have:
```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    [HttpGet]
    public ActionResult<IEnumerable<Product>> GetProducts() {...}    

    [HttpGet("{id}")]
    public ActionResult<Product> GetProduct(int id) {...}

    [HttpPost]
    public ActionResult<Product> CreateProduct(Product product) {...}

    [HttpPut("{id}")]
    public IActionResult UpdateProduct(int id, Product product) {...}

    [HttpDelete("{id}")]
    public IActionResult DeleteProduct(int id) {...}
}
```

This is the basic CRUD controller.
```text
GET     -> Read
GET /id -> Read one
POST    -> Create
PUT     -> Update
DELETE  -> Delete
```

But we shouldn't put everything in the controller. At first, this is fine:
```csharp
public IActionResult CreateProduct(Product product) {...}
```

But when the application becomes large, the controller might eventually contain:
```text
validation
business rules
database queries
calculations
authentication logic
email sending
logging
external API calls
etc.
```

Then the controller becomes enormous. Instead, we separate responsibilities:

```text
Controller
   receives HTTP request
    v
Service
   business logic
    v
Repository / DbContext
   db operations
    v
   DB
```

# 14. Let's write a service

```csharp
// dir: Services/IProductService.cs
using ProductApi.Models;

namespace ProductApi.Services;

public interface IProductService
{
    Task<IEnumerable<Product>> GetProductsAsync();
    Task<Product?> GetProductAsync(int id);
    Task<Product> CreateProductAsync(Product product);
    Task UpdateProductAsync(Product product);
    Task DeleteProductAsync(int id);
}
```

The interface describes what the service can do. It does not describe exactly how it does it:
```text
"Any product service must know how to:
 - get products
 - get one product
 - create
 - update
 - delete"
```

```csharp
// dir: Services/ProductService.cs
using ProductApi.Models;

namespace ProductApi.Services;

public class ProductService : IProductService
{
    private readonly List<Product> _products = new()
    {
        new Product {...},
        new Product {...}
    };

    public Task<IEnumerable<Product>> GetProductsAsync()
    {
        return Task.FromResult<IEnumerable<Product>>(_products);
    }

    public Task<Product?> GetProductAsync(int id)
    {
        var product = _products.FirstOrDefault(p => p.Id == id);
        return Task.FromResult(product);
    }

    public Task<Product> CreateProductAsync(Product product)
    {
        product.Id = _products.Max(p => p.Id) + 1;
        _products.Add(product);

        return Task.FromResult(product);
    }

    public Task UpdateProductAsync(Product product)
    {
        var existing = _products.First(p => p.Id == product.Id);

        existing.Name = product.Name;
        existing.Price = product.Price;

        return Task.CompletedTask;
    }

    public Task DeleteProductAsync(int id)
    {
        var product = _products.First(p => p.Id == id);
        _products.Remove(product);

        return Task.CompletedTask;
    }
}
```

The important point isn't implementation, it's the architecture: `Controller -> IProductService -> ProductService -> Data`. Later, `ProductService` could communicate with Entity Framework Core (EF Core) and a real db without requiring the controller to know how the database works.

Inside `Program.cs`, add:
```csharp
builder.Services.AddScoped<IProductService, ProductService>();
```

So:
```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();

// DI
builder.Services.AddScoped<IProductService, ProductService>();

var app = builder.Build();

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

`AddScoped` specifies the service lifetime. 3 important lifetimes are:
```text
Transient -> new instance each time it is requested.
Scoped -> one instance per HTTP request.
Singleton -> one instance for the application's lifetime.
```

For services that work with a request and especially database contexts, `Scoped` is commonly used.

Now the controller becomes:
```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly IProductService _productService;

    public ProductsController(IProductService productService)
    {
        _productService = productService;
    }
}
```

The constructor says: **"I need an IProductService"**. ASP.NET Core's DI container supplies the implementation we registered. This is **constructor injection**.

Now our controller can use the service asynchronously:
```csharp
[HttpGet]
public async Task<ActionResult<IEnumerable<Product>>> GetProducts()
{
    var products = await _productService.GetProductsAsync();
    return Ok(products);
}
```

Notice:
```text
HTTP request
     v
Controller
     await
     v
Service
     await
     v
    DB
```

This is exactly where the [[Asynchronous programming|async concepts]] we learned earlier become useful. When the DB is doing I/O, we don't want the server thread to simply sit there blocked waiting for the database.

Our controller can now look like:
```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly IProductService _productService;

    public ProductsController(IProductService productService) =>
        _productService = productService;

    [HttpGet]
    public async Task<ActionResult<IEnumerable<Product>>> GetProducts() =>
        Ok(await _productService.GetProductsAsync());

    [HttpGet("{id}")]
    public async Task<ActionResult<Product>> GetProduct(int id)
    {
        var product = await _productService.GetProductAsync(id);

        if (product == null) return NotFound();
        
        return Ok(product);
    }

    [HttpPost]
    public async Task<ActionResult<Product>> CreateProduct(Product product)
    {
        var created = await _productService.CreateProductAsync(product);

        return CreatedAtAction(
            nameof(GetProduct),
            new { id = created.Id },
            created
        );
    }

    [HttpPut("{id}")]
    public async Task<IActionResult> UpdateProduct(int id, Product product)
    {
        if (id != product.Id) return BadRequest();
        await _productService.UpdateProductAsync(product);

        return NoContent();
    }

    [HttpDelete("{id}")]
    public async Task<IActionResult> DeleteProduct(int id)
    {
        await _productService.DeleteProductAsync(id);
        return NoContent();
    }
}
```

Now the controller has a much clearer responsibility:
```text
HTTP request
     v
Controller
    translates HTTP -> application operation
     v
Service
    handles business logic
     v
Data
```

# 15. Add a real database

A common choice in .NET is **EF Core**. The architecture becomes: `Controller -> Service -> DbContext -> EF Core -> DB`
```csharp
// dir: Data/AppDbContext.cs
using Microsoft.EntityFrameworkCore;
using ProductApi.Models;

namespace ProductApi.Data;

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) {}
    public DbSet<Product> Products => Set<Product>();
}
```

- `DbContext` is the main EF Core object through which our application works with the database.
- `DbSet<Product>` represents the collection of `Product` entities in the database.

The exact DB configuration depends on the database we choose. We'll use SQL Server:
```csharp
builder.Services.AddDbContext<AppDbContext>(options => options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));
```

Now `AppDbContext` is registered with DI. The configuration might live in `appsettings.json`, we don't want to hard-code configuration directly into our controller.
```json
{
    "ConnectionStrings": {
        "DefaultConnection": "..."
    }
}
```

With EF Core, DB operations commonly have async versions:
```csharp
await _context.Products.ToListAsync();
await _context.Products.FindAsync(id);
await _context.SaveChangesAsync();
```

So a service might contain:
```csharp
public async Task<IEnumerable<Product>> GetProductsAsync() =>
    await _context.Products.ToListAsync();
    
public async Task<Product?> GetProductAsync(int id) => 
	await _context.Products.FindAsync(id);
```

This is where async/await is particularly useful in Web APIs because database and network operations are I/O-bound:
```text
HTTP request
      v
Controller
      await
      v
Service
      await
      v
EF Core
      await
      v
	  DB
```

# 16. DTOs

Our API currently accepts and returns `Product` directly. That can become problematic in larger applications. Instead, we can create DTOs which describe the data we want to send or receive through the API since the client doesn't necessarily need to know about every property in our internal `Product` model.
```csharp
// dir: DTOs/ProductDto.cs
namespace ProductApi.DTOs;

public class ProductDto
{
    public string Name { get; set; } = string.Empty;
    public decimal Price { get; set; }
}
```

We can separate:
```text
Database/domain model -> Product
API representation -> ProductDto
```

This becomes especially important when the internal model contains sensitive properties that should not be exposed to clients.

# 17. Validation

We can use validation attributes/ annotations:
```csharp
using System.ComponentModel.DataAnnotations;

public class CreateProductDto
{
    [Required]
    public string Name { get; set; } = string.Empty;

    [Range(0.01, double.MaxValue)]
    public decimal Price { get; set; }
}
```

Then:
```csharp
[HttpPost]
public async Task<ActionResult<Product>> CreateProduct(CreateProductDto product)
{
    // ...
}
```

Because the controller has `[ApiController]`, ASP.NET Core automatically performs model validation and can return a `400 Bad Request` when validation fails.

# 18. Middleware

```csharp
app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
```

These are part of the ASP.NET Core request pipeline. Middleware is code that participates in processing HTTP requests and responses. it can inspect or modify requests and responses. Think of middleware as checkpoints. For example:
```text
Request
   v
HTTPS redirection
   v
CORS
   v
Authentication
   v
Authorization
   v
Controller
```

# 19. CORS

This is an extremely important web API concept. Suppose our API runs at `https://localhost:7000` and our frontend runs at `https://localhost:2000`

These are different **origins** because the scheme, host, or port differs. Browsers enforce the **same-origin policy**, which restricts browser JavaScript from making certain requests to a different origin.

CORS stands for `Cross-Origin Resource Sharing`, itis primarily a **browser security mechanism**. It allows our API to explicitly say: "I allow requests from this origin.

In `Program.cs`:
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("FrontendPolicy", policy =>
    {
        policy.WithOrigins("http://localhost:3000")
	        .AllowAnyHeader()
            .AllowAnyMethod();
    });
});
```

Then in the middleware pipeline:
```csharp
app.UseHttpsRedirection();
app.UseRouting();
app.UseCors("FrontendPolicy");
app.UseAuthorization();
app.MapControllers();
```

The important relationship is:
```text
FE: http://localhost:3000
       request
        v
API: https://localhost:7000
        v
CORS policy
        v
"localhost:3000 is allowed"
```

ASP.NET Core's CORS middleware should be placed **after routing** and **before authorization** in the common middleware configuration.

# 20. OpenAPI and Swagger

When developing an API, manually typing every HTTP request gets annoying. OpenAPI describes our API in a machine-readable format. Tools such as Swagger UI can then give us an interactive interface for testing endpoints.
```text
Controller
    v
OpenAPI description
    v
Swagger UI
    v
GET / POST / PUT / DELETE
```

So instead of manually creating requests, we can open Swagger and test:
```text
GET /api/products
POST /api/products
GET /api/products/{id}
PUT /api/products/{id}
DELETE /api/products/{id}
```

# 21. Overview

Suppose the frontend sends: `GET /api/products/5`

The request enters our API:
```text
HTTP Request
     v
ASP.NET Core server
     v
Middleware pipeline
     v
Routing
     v
ProductsController
     v
GetProduct(5)
     v
ProductService
     v
EF Core
     v
    DB
```

Then the result travels back:
```text
    DB
    v
EF Core
    v
ProductService
    v
ProductsController
    v
HTTP Response
    v
    FE
```

Our app now looks conceptually like:
```text
                    ASP.NET CORE
                         v
              ┌─────────────────────┐
         Middleware              Routing
              └─────────────────────┘
                         v
                   Controller
                         v
                     Service
                         v
                    DbContext
                         v
                         DB
```

And the project structure can look like:
```text
ProductApi/
│
├── Controllers/
│   └── ProductsController.cs
│
├── Models/
│   └── Product.cs
│
├── DTOs/
│   ├── CreateProductDto.cs
│   └── ProductDto.cs
│
├── Services/
│   ├── IProductService.cs
│   └── ProductService.cs
│
├── Data/
│   └── AppDbContext.cs
│
├── Properties/
│   └── launchSettings.json
│
├── appsettings.json
├── appsettings.Development.json
├── Program.cs
└── ProductApi.csproj
```

The easiest way to remember the architecture is:
```text
Program.cs -> builds and configures the application.
Middleware -> processes HTTP requests/responses.
Controller -> handles HTTP requests and produces HTTP responses.
DTO -> defines the shape of API input/output.
Service -> contains application/business logic.
DbContext -> communicates with the database through EF Core.
Model -> represents application/domain data.
DB -> permanently stores the data.
```
# 22. In a nutshell

Understand the pipeline:
1. Client sends HTTP request.
2. ASP.NET Core receives the request.
3. Middleware processes the request.
4. Routing determines which controller/action handles it.
5. Model binding converts HTTP data into C# values/objects.
6. Controller receives the request.
7. Controller calls a service.
8. Service performs business logic.
9. Service communicates with the database.
10. Database returns data.
11. Service returns the data.
12. Controller creates an HTTP response.
13. ASP.NET Core sends the response to the client.

```text
CLIENT
  |  HTTP
  v
ASP.NET CORE
  +--> Middleware
  +--> CORS
  +--> Routing
  v
CONTROLLER
  |  await
  v
SERVICE
  |  await
  v
  DB
  v
SERVICE
  v
CONTROLLER
  v
HTTP RESPONSE
  v
CLIENT
```

Terms:
- ASP.NET Core = framework for building web applications and APIs.
- Web API = HTTP-based backend that other applications can communicate with.
- Controller = receives HTTP requests and returns HTTP responses.
- Route = maps URLs and HTTP methods to controller actions.
- Model = represents application/domain data.
- DTO = represents data transferred through the API.
- Service = contains application/business logic.
- Dependency Injection = gives classes the dependencies they need.
- DbContext = EF Core's main gateway to the database.
- Middleware = components that participate in the HTTP request/response pipeline.
- CORS = browser security mechanism that lets an API specify which different origins can make cross-origin requests.
- async/await = allows I/O-bound operations to be awaited without blocking the request thread while waiting