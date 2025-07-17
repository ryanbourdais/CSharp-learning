# Project 11: API Development

## Assignment Summary
Create a REST API for a simple inventory management system with endpoints for products, categories, and orders. This project introduces API development concepts, JSON serialization, HTTP methods, and API testing.

## Learning Objectives
- Understand REST API principles and design
- Implement HTTP methods (GET, POST, PUT, DELETE)
- Work with JSON serialization and deserialization
- Create API controllers and endpoints
- Implement proper HTTP status codes
- Practice API testing and documentation

## Project Requirements

### Basic Requirements
1. **Product Management**: CRUD operations for products
2. **Category Management**: CRUD operations for categories
3. **Order Management**: CRUD operations for orders
4. **RESTful Design**: Follow REST principles
5. **JSON Responses**: Proper JSON formatting
6. **HTTP Status Codes**: Appropriate status codes

### Advanced Requirements (Optional)
- Add authentication and authorization
- Implement pagination for large datasets
- Add filtering and sorting capabilities
- Create API documentation with Swagger
- Add request/response validation
- Implement rate limiting

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new webapi -n InventoryAPI
cd InventoryAPI
```

### Step 2: Install Required Packages
```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Swashbuckle.AspNetCore
```

### Step 3: Create Data Models
```csharp
// Models/Product.cs
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public decimal Price { get; set; }
    public int StockQuantity { get; set; }
    public int CategoryId { get; set; }
    public DateTime CreatedDate { get; set; }
    public DateTime? UpdatedDate { get; set; }
    
    // Navigation property
    public Category Category { get; set; }
    public ICollection<OrderItem> OrderItems { get; set; } = new List<OrderItem>();
}

// Models/Category.cs
public class Category
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public DateTime CreatedDate { get; set; }
    
    // Navigation property
    public ICollection<Product> Products { get; set; } = new List<Product>();
}

// Models/Order.cs
public class Order
{
    public int Id { get; set; }
    public string CustomerName { get; set; }
    public string CustomerEmail { get; set; }
    public DateTime OrderDate { get; set; }
    public string Status { get; set; } // Pending, Shipped, Delivered, Cancelled
    public decimal TotalAmount { get; set; }
    
    // Navigation property
    public ICollection<OrderItem> OrderItems { get; set; } = new List<OrderItem>();
}

// Models/OrderItem.cs
public class OrderItem
{
    public int Id { get; set; }
    public int OrderId { get; set; }
    public int ProductId { get; set; }
    public int Quantity { get; set; }
    public decimal UnitPrice { get; set; }
    
    // Navigation properties
    public Order Order { get; set; }
    public Product Product { get; set; }
}
```

## Hints and Code Examples

### Database Context
```csharp
// Data/InventoryContext.cs
public class InventoryContext : DbContext
{
    public InventoryContext(DbContextOptions<InventoryContext> options) : base(options)
    {
    }
    
    public DbSet<Product> Products { get; set; }
    public DbSet<Category> Categories { get; set; }
    public DbSet<Order> Orders { get; set; }
    public DbSet<OrderItem> OrderItems { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Configure relationships
        modelBuilder.Entity<Product>()
            .HasOne(p => p.Category)
            .WithMany(c => c.Products)
            .HasForeignKey(p => p.CategoryId);
            
        modelBuilder.Entity<OrderItem>()
            .HasOne(oi => oi.Order)
            .WithMany(o => o.OrderItems)
            .HasForeignKey(oi => oi.OrderId);
            
        modelBuilder.Entity<OrderItem>()
            .HasOne(oi => oi.Product)
            .WithMany(p => p.OrderItems)
            .HasForeignKey(oi => oi.ProductId);
    }
}
```

### Product Controller
```csharp
// Controllers/ProductsController.cs
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly InventoryContext _context;
    private readonly ILogger<ProductsController> _logger;
    
    public ProductsController(InventoryContext context, ILogger<ProductsController> logger)
    {
        _context = context;
        _logger = logger;
    }
    
    // GET: api/products
    [HttpGet]
    public async Task<ActionResult<IEnumerable<Product>>> GetProducts()
    {
        try
        {
            var products = await _context.Products
                .Include(p => p.Category)
                .ToListAsync();
                
            return Ok(products);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error retrieving products");
            return StatusCode(500, "Internal server error");
        }
    }
    
    // GET: api/products/5
    [HttpGet("{id}")]
    public async Task<ActionResult<Product>> GetProduct(int id)
    {
        var product = await _context.Products
            .Include(p => p.Category)
            .FirstOrDefaultAsync(p => p.Id == id);
            
        if (product == null)
        {
            return NotFound();
        }
        
        return Ok(product);
    }
    
    // POST: api/products
    [HttpPost]
    public async Task<ActionResult<Product>> CreateProduct(Product product)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }
        
        // Validate category exists
        var category = await _context.Categories.FindAsync(product.CategoryId);
        if (category == null)
        {
            return BadRequest("Invalid category ID");
        }
        
        product.CreatedDate = DateTime.UtcNow;
        
        _context.Products.Add(product);
        await _context.SaveChangesAsync();
        
        return CreatedAtAction(nameof(GetProduct), new { id = product.Id }, product);
    }
    
    // PUT: api/products/5
    [HttpPut("{id}")]
    public async Task<IActionResult> UpdateProduct(int id, Product product)
    {
        if (id != product.Id)
        {
            return BadRequest();
        }
        
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }
        
        var existingProduct = await _context.Products.FindAsync(id);
        if (existingProduct == null)
        {
            return NotFound();
        }
        
        // Validate category exists
        var category = await _context.Categories.FindAsync(product.CategoryId);
        if (category == null)
        {
            return BadRequest("Invalid category ID");
        }
        
        existingProduct.Name = product.Name;
        existingProduct.Description = product.Description;
        existingProduct.Price = product.Price;
        existingProduct.StockQuantity = product.StockQuantity;
        existingProduct.CategoryId = product.CategoryId;
        existingProduct.UpdatedDate = DateTime.UtcNow;
        
        try
        {
            await _context.SaveChangesAsync();
        }
        catch (DbUpdateConcurrencyException)
        {
            if (!ProductExists(id))
            {
                return NotFound();
            }
            else
            {
                throw;
            }
        }
        
        return NoContent();
    }
    
    // DELETE: api/products/5
    [HttpDelete("{id}")]
    public async Task<IActionResult> DeleteProduct(int id)
    {
        var product = await _context.Products.FindAsync(id);
        if (product == null)
        {
            return NotFound();
        }
        
        _context.Products.Remove(product);
        await _context.SaveChangesAsync();
        
        return NoContent();
    }
    
    // GET: api/products/search?name=keyword
    [HttpGet("search")]
    public async Task<ActionResult<IEnumerable<Product>>> SearchProducts([FromQuery] string name)
    {
        if (string.IsNullOrWhiteSpace(name))
        {
            return BadRequest("Search term is required");
        }
        
        var products = await _context.Products
            .Include(p => p.Category)
            .Where(p => p.Name.Contains(name) || p.Description.Contains(name))
            .ToListAsync();
            
        return Ok(products);
    }
    
    private bool ProductExists(int id)
    {
        return _context.Products.Any(e => e.Id == id);
    }
}
```

### Order Controller
```csharp
// Controllers/OrdersController.cs
[ApiController]
[Route("api/[controller]")]
public class OrdersController : ControllerBase
{
    private readonly InventoryContext _context;
    private readonly ILogger<OrdersController> _logger;
    
    public OrdersController(InventoryContext context, ILogger<OrdersController> logger)
    {
        _context = context;
        _logger = logger;
    }
    
    // GET: api/orders
    [HttpGet]
    public async Task<ActionResult<IEnumerable<Order>>> GetOrders()
    {
        var orders = await _context.Orders
            .Include(o => o.OrderItems)
            .ThenInclude(oi => oi.Product)
            .ToListAsync();
            
        return Ok(orders);
    }
    
    // GET: api/orders/5
    [HttpGet("{id}")]
    public async Task<ActionResult<Order>> GetOrder(int id)
    {
        var order = await _context.Orders
            .Include(o => o.OrderItems)
            .ThenInclude(oi => oi.Product)
            .FirstOrDefaultAsync(o => o.Id == id);
            
        if (order == null)
        {
            return NotFound();
        }
        
        return Ok(order);
    }
    
    // POST: api/orders
    [HttpPost]
    public async Task<ActionResult<Order>> CreateOrder(OrderCreateDto orderDto)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }
        
        // Validate all products exist and have sufficient stock
        foreach (var item in orderDto.OrderItems)
        {
            var product = await _context.Products.FindAsync(item.ProductId);
            if (product == null)
            {
                return BadRequest($"Product with ID {item.ProductId} not found");
            }
            
            if (product.StockQuantity < item.Quantity)
            {
                return BadRequest($"Insufficient stock for product {product.Name}");
            }
        }
        
        var order = new Order
        {
            CustomerName = orderDto.CustomerName,
            CustomerEmail = orderDto.CustomerEmail,
            OrderDate = DateTime.UtcNow,
            Status = "Pending",
            TotalAmount = 0
        };
        
        _context.Orders.Add(order);
        await _context.SaveChangesAsync();
        
        // Create order items and calculate total
        decimal totalAmount = 0;
        foreach (var itemDto in orderDto.OrderItems)
        {
            var product = await _context.Products.FindAsync(itemDto.ProductId);
            
            var orderItem = new OrderItem
            {
                OrderId = order.Id,
                ProductId = itemDto.ProductId,
                Quantity = itemDto.Quantity,
                UnitPrice = product.Price
            };
            
            _context.OrderItems.Add(orderItem);
            
            // Update product stock
            product.StockQuantity -= itemDto.Quantity;
            
            totalAmount += product.Price * itemDto.Quantity;
        }
        
        order.TotalAmount = totalAmount;
        await _context.SaveChangesAsync();
        
        return CreatedAtAction(nameof(GetOrder), new { id = order.Id }, order);
    }
    
    // PUT: api/orders/5/status
    [HttpPut("{id}/status")]
    public async Task<IActionResult> UpdateOrderStatus(int id, [FromBody] string status)
    {
        var order = await _context.Orders.FindAsync(id);
        if (order == null)
        {
            return NotFound();
        }
        
        var validStatuses = new[] { "Pending", "Shipped", "Delivered", "Cancelled" };
        if (!validStatuses.Contains(status))
        {
            return BadRequest("Invalid status");
        }
        
        order.Status = status;
        await _context.SaveChangesAsync();
        
        return NoContent();
    }
}

// DTOs for API requests/responses
public class OrderCreateDto
{
    public string CustomerName { get; set; }
    public string CustomerEmail { get; set; }
    public List<OrderItemDto> OrderItems { get; set; }
}

public class OrderItemDto
{
    public int ProductId { get; set; }
    public int Quantity { get; set; }
}
```

### Program.cs Configuration
```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Add DbContext
builder.Services.AddDbContext<InventoryContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

### API Testing Examples
```bash
# Get all products
curl -X GET "https://localhost:7001/api/products"

# Get product by ID
curl -X GET "https://localhost:7001/api/products/1"

# Create new product
curl -X POST "https://localhost:7001/api/products" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Laptop",
    "description": "High-performance laptop",
    "price": 999.99,
    "stockQuantity": 10,
    "categoryId": 1
  }'

# Update product
curl -X PUT "https://localhost:7001/api/products/1" \
  -H "Content-Type: application/json" \
  -d '{
    "id": 1,
    "name": "Updated Laptop",
    "description": "Updated description",
    "price": 1099.99,
    "stockQuantity": 5,
    "categoryId": 1
  }'

# Delete product
curl -X DELETE "https://localhost:7001/api/products/1"

# Search products
curl -X GET "https://localhost:7001/api/products/search?name=laptop"

# Create order
curl -X POST "https://localhost:7001/api/orders" \
  -H "Content-Type: application/json" \
  -d '{
    "customerName": "John Doe",
    "customerEmail": "john@example.com",
    "orderItems": [
      {
        "productId": 1,
        "quantity": 2
      }
    ]
  }'
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Not using proper HTTP status codes
[HttpPost]
public IActionResult CreateProduct(Product product)
{
    _context.Products.Add(product);
    _context.SaveChanges();
    return Ok(product); // Should return 201 Created
}

// Not handling exceptions
public async Task<ActionResult<Product>> GetProduct(int id)
{
    var product = await _context.Products.FindAsync(id);
    return Ok(product); // Could be null
}

// Not validating input
[HttpPost]
public IActionResult CreateProduct(Product product)
{
    _context.Products.Add(product); // No validation
    _context.SaveChanges();
    return Ok(product);
}

// Not using async/await properly
public ActionResult<IEnumerable<Product>> GetProducts()
{
    var products = _context.Products.ToList(); // Blocking call
    return Ok(products);
}
```

### ✅ Do This Instead:
```csharp
// Use proper HTTP status codes
[HttpPost]
public async Task<ActionResult<Product>> CreateProduct(Product product)
{
    if (!ModelState.IsValid)
        return BadRequest(ModelState);
        
    _context.Products.Add(product);
    await _context.SaveChangesAsync();
    return CreatedAtAction(nameof(GetProduct), new { id = product.Id }, product);
}

// Handle exceptions and null values
public async Task<ActionResult<Product>> GetProduct(int id)
{
    var product = await _context.Products.FindAsync(id);
    if (product == null)
        return NotFound();
        
    return Ok(product);
}

// Validate input
[HttpPost]
public async Task<ActionResult<Product>> CreateProduct(Product product)
{
    if (!ModelState.IsValid)
        return BadRequest(ModelState);
        
    // Additional validation
    if (product.Price < 0)
        return BadRequest("Price cannot be negative");
        
    _context.Products.Add(product);
    await _context.SaveChangesAsync();
    return CreatedAtAction(nameof(GetProduct), new { id = product.Id }, product);
}

// Use async/await properly
public async Task<ActionResult<IEnumerable<Product>>> GetProducts()
{
    var products = await _context.Products.ToListAsync();
    return Ok(products);
}
```

## Testing Your API

### Test Cases to Try:
1. **GET requests**: Retrieve all products, categories, orders
2. **POST requests**: Create new products, categories, orders
3. **PUT requests**: Update existing resources
4. **DELETE requests**: Remove resources
5. **Validation**: Try invalid data → Should return 400 Bad Request
6. **Not found**: Request non-existent resources → Should return 404
7. **Swagger UI**: Test API through Swagger interface

### How to Run:
```bash
dotnet run
```
Then open `https://localhost:7001/swagger` in your browser.

## Submission Checklist
- [ ] Project compiles without errors
- [ ] All CRUD operations work for products, categories, orders
- [ ] Proper HTTP status codes are returned
- [ ] JSON responses are correctly formatted
- [ ] Input validation is implemented
- [ ] Error handling is in place
- [ ] Swagger documentation is available
- [ ] API follows REST principles
- [ ] Code is properly structured and documented

## Bonus Challenges
1. **Authentication**: Add JWT token authentication
2. **Pagination**: Implement pagination for large datasets
3. **Filtering**: Add filtering and sorting capabilities
4. **Rate Limiting**: Implement API rate limiting
5. **Caching**: Add response caching
6. **Logging**: Implement comprehensive API logging
7. **Versioning**: Add API versioning
8. **Testing**: Create unit tests for API endpoints

## Resources
- [ASP.NET Core Web API](https://docs.microsoft.com/en-us/aspnet/core/web-api/)
- [REST API Design](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design)
- [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [Swagger Documentation](https://swagger.io/docs/)

## Next Steps
Once you complete this project, you'll be ready for Project 12: Real-World Application, where you'll build a complete application using all the skills learned throughout the course. 