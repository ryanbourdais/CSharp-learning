# Project 12: Real-World Application

## Assignment Summary
Build a complete real-world application that combines all the skills you've learned throughout the course. Choose from several project options or create your own, implementing a full-stack solution with database, API, web interface, and deployment considerations.

## Learning Objectives
- Apply all previously learned concepts in a real project
- Implement complete application lifecycle
- Practice project planning and architecture
- Work with testing and deployment
- Handle real-world problem solving
- Build a portfolio-worthy project

## Project Options

### Option 1: Recipe Manager with Meal Planning
A comprehensive recipe management system with meal planning, shopping lists, and nutritional tracking.

### Option 2: Personal Task Manager with Reminders
A sophisticated task management system with reminders, categories, and progress tracking.

### Option 3: Simple E-commerce Platform
A complete online store with products, shopping cart, orders, and payment integration.

### Option 4: Blog Platform
A full-featured blog system with posts, comments, user management, and content management.

## Project Requirements

### Basic Requirements
1. **Full-Stack Application**: Complete frontend and backend
2. **Database Design**: Proper data modeling and relationships
3. **API Development**: RESTful API for data operations
4. **Web Interface**: User-friendly web application
5. **Authentication**: User registration and login
6. **Testing**: Unit tests and integration tests
7. **Documentation**: Complete project documentation
8. **Deployment**: Deploy to cloud platform

### Advanced Requirements (Optional)
- Add real-time features (SignalR)
- Implement advanced search and filtering
- Add file upload capabilities
- Create mobile-responsive design
- Add third-party integrations
- Implement caching strategies
- Add monitoring and logging
- Create CI/CD pipeline

## Step-by-Step Instructions

### Step 1: Project Planning
1. **Choose Your Project**: Select one of the options or create your own
2. **Define Requirements**: List all features and functionality
3. **Design Architecture**: Plan the overall system architecture
4. **Create Database Schema**: Design your data models
5. **Plan API Endpoints**: Define your REST API structure
6. **Design UI/UX**: Plan the user interface

### Step 2: Project Setup
```bash
# Create solution and projects
dotnet new sln -n RealWorldApp
dotnet new webapi -n RealWorldApp.API
dotnet new mvc -n RealWorldApp.Web
dotnet new classlib -n RealWorldApp.Core
dotnet new classlib -n RealWorldApp.Infrastructure
dotnet new xunit -n RealWorldApp.Tests

# Add projects to solution
dotnet sln add RealWorldApp.API/RealWorldApp.API.csproj
dotnet sln add RealWorldApp.Web/RealWorldApp.Web.csproj
dotnet sln add RealWorldApp.Core/RealWorldApp.Core.csproj
dotnet sln add RealWorldApp.Infrastructure/RealWorldApp.Infrastructure.csproj
dotnet sln add RealWorldApp.Tests/RealWorldApp.Tests.csproj
```

### Step 3: Example: Recipe Manager Implementation

#### Core Models
```csharp
// RealWorldApp.Core/Models/User.cs
public class User
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string Email { get; set; }
    public string PasswordHash { get; set; }
    public DateTime CreatedDate { get; set; }
    
    // Navigation properties
    public ICollection<Recipe> Recipes { get; set; } = new List<Recipe>();
    public ICollection<MealPlan> MealPlans { get; set; } = new List<MealPlan>();
}

// RealWorldApp.Core/Models/Recipe.cs
public class Recipe
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Description { get; set; }
    public int PrepTime { get; set; } // minutes
    public int CookTime { get; set; } // minutes
    public int Servings { get; set; }
    public string Instructions { get; set; }
    public string ImageUrl { get; set; }
    public int UserId { get; set; }
    public DateTime CreatedDate { get; set; }
    
    // Navigation properties
    public User User { get; set; }
    public ICollection<RecipeIngredient> RecipeIngredients { get; set; } = new List<RecipeIngredient>();
    public ICollection<MealPlanItem> MealPlanItems { get; set; } = new List<MealPlanItem>();
}

// RealWorldApp.Core/Models/Ingredient.cs
public class Ingredient
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Unit { get; set; } // grams, cups, etc.
    public decimal CaloriesPerUnit { get; set; }
    
    // Navigation properties
    public ICollection<RecipeIngredient> RecipeIngredients { get; set; } = new List<RecipeIngredient>();
}

// RealWorldApp.Core/Models/RecipeIngredient.cs
public class RecipeIngredient
{
    public int Id { get; set; }
    public int RecipeId { get; set; }
    public int IngredientId { get; set; }
    public decimal Quantity { get; set; }
    
    // Navigation properties
    public Recipe Recipe { get; set; }
    public Ingredient Ingredient { get; set; }
}

// RealWorldApp.Core/Models/MealPlan.cs
public class MealPlan
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime StartDate { get; set; }
    public DateTime EndDate { get; set; }
    public int UserId { get; set; }
    
    // Navigation properties
    public User User { get; set; }
    public ICollection<MealPlanItem> MealPlanItems { get; set; } = new List<MealPlanItem>();
}

// RealWorldApp.Core/Models/MealPlanItem.cs
public class MealPlanItem
{
    public int Id { get; set; }
    public int MealPlanId { get; set; }
    public int RecipeId { get; set; }
    public DateTime Date { get; set; }
    public string MealType { get; set; } // Breakfast, Lunch, Dinner, Snack
    
    // Navigation properties
    public MealPlan MealPlan { get; set; }
    public Recipe Recipe { get; set; }
}
```

#### Infrastructure Layer
```csharp
// RealWorldApp.Infrastructure/Data/ApplicationDbContext.cs
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options)
    {
    }
    
    public DbSet<User> Users { get; set; }
    public DbSet<Recipe> Recipes { get; set; }
    public DbSet<Ingredient> Ingredients { get; set; }
    public DbSet<RecipeIngredient> RecipeIngredients { get; set; }
    public DbSet<MealPlan> MealPlans { get; set; }
    public DbSet<MealPlanItem> MealPlanItems { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Configure relationships
        modelBuilder.Entity<RecipeIngredient>()
            .HasOne(ri => ri.Recipe)
            .WithMany(r => r.RecipeIngredients)
            .HasForeignKey(ri => ri.RecipeId);
            
        modelBuilder.Entity<RecipeIngredient>()
            .HasOne(ri => ri.Ingredient)
            .WithMany(i => i.RecipeIngredients)
            .HasForeignKey(ri => ri.IngredientId);
            
        modelBuilder.Entity<MealPlanItem>()
            .HasOne(mpi => mpi.MealPlan)
            .WithMany(mp => mp.MealPlanItems)
            .HasForeignKey(mpi => mpi.MealPlanId);
            
        modelBuilder.Entity<MealPlanItem>()
            .HasOne(mpi => mpi.Recipe)
            .WithMany(r => r.MealPlanItems)
            .HasForeignKey(mpi => mpi.RecipeId);
    }
}

// RealWorldApp.Infrastructure/Services/IRecipeService.cs
public interface IRecipeService
{
    Task<IEnumerable<Recipe>> GetAllRecipesAsync();
    Task<Recipe> GetRecipeByIdAsync(int id);
    Task<Recipe> CreateRecipeAsync(Recipe recipe);
    Task<Recipe> UpdateRecipeAsync(Recipe recipe);
    Task DeleteRecipeAsync(int id);
    Task<IEnumerable<Recipe>> SearchRecipesAsync(string searchTerm);
    Task<IEnumerable<Recipe>> GetRecipesByUserAsync(int userId);
}

// RealWorldApp.Infrastructure/Services/RecipeService.cs
public class RecipeService : IRecipeService
{
    private readonly ApplicationDbContext _context;
    
    public RecipeService(ApplicationDbContext context)
    {
        _context = context;
    }
    
    public async Task<IEnumerable<Recipe>> GetAllRecipesAsync()
    {
        return await _context.Recipes
            .Include(r => r.User)
            .Include(r => r.RecipeIngredients)
            .ThenInclude(ri => ri.Ingredient)
            .ToListAsync();
    }
    
    public async Task<Recipe> GetRecipeByIdAsync(int id)
    {
        return await _context.Recipes
            .Include(r => r.User)
            .Include(r => r.RecipeIngredients)
            .ThenInclude(ri => ri.Ingredient)
            .FirstOrDefaultAsync(r => r.Id == id);
    }
    
    public async Task<Recipe> CreateRecipeAsync(Recipe recipe)
    {
        recipe.CreatedDate = DateTime.UtcNow;
        _context.Recipes.Add(recipe);
        await _context.SaveChangesAsync();
        return recipe;
    }
    
    public async Task<Recipe> UpdateRecipeAsync(Recipe recipe)
    {
        var existingRecipe = await _context.Recipes.FindAsync(recipe.Id);
        if (existingRecipe == null)
            return null;
            
        existingRecipe.Title = recipe.Title;
        existingRecipe.Description = recipe.Description;
        existingRecipe.PrepTime = recipe.PrepTime;
        existingRecipe.CookTime = recipe.CookTime;
        existingRecipe.Servings = recipe.Servings;
        existingRecipe.Instructions = recipe.Instructions;
        existingRecipe.ImageUrl = recipe.ImageUrl;
        
        await _context.SaveChangesAsync();
        return existingRecipe;
    }
    
    public async Task DeleteRecipeAsync(int id)
    {
        var recipe = await _context.Recipes.FindAsync(id);
        if (recipe != null)
        {
            _context.Recipes.Remove(recipe);
            await _context.SaveChangesAsync();
        }
    }
    
    public async Task<IEnumerable<Recipe>> SearchRecipesAsync(string searchTerm)
    {
        return await _context.Recipes
            .Include(r => r.User)
            .Where(r => r.Title.Contains(searchTerm) || r.Description.Contains(searchTerm))
            .ToListAsync();
    }
    
    public async Task<IEnumerable<Recipe>> GetRecipesByUserAsync(int userId)
    {
        return await _context.Recipes
            .Include(r => r.RecipeIngredients)
            .ThenInclude(ri => ri.Ingredient)
            .Where(r => r.UserId == userId)
            .ToListAsync();
    }
}
```

#### API Controllers
```csharp
// RealWorldApp.API/Controllers/RecipesController.cs
[ApiController]
[Route("api/[controller]")]
public class RecipesController : ControllerBase
{
    private readonly IRecipeService _recipeService;
    private readonly ILogger<RecipesController> _logger;
    
    public RecipesController(IRecipeService recipeService, ILogger<RecipesController> logger)
    {
        _recipeService = recipeService;
        _logger = logger;
    }
    
    [HttpGet]
    public async Task<ActionResult<IEnumerable<Recipe>>> GetRecipes()
    {
        try
        {
            var recipes = await _recipeService.GetAllRecipesAsync();
            return Ok(recipes);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error retrieving recipes");
            return StatusCode(500, "Internal server error");
        }
    }
    
    [HttpGet("{id}")]
    public async Task<ActionResult<Recipe>> GetRecipe(int id)
    {
        var recipe = await _recipeService.GetRecipeByIdAsync(id);
        if (recipe == null)
            return NotFound();
            
        return Ok(recipe);
    }
    
    [HttpPost]
    public async Task<ActionResult<Recipe>> CreateRecipe(Recipe recipe)
    {
        if (!ModelState.IsValid)
            return BadRequest(ModelState);
            
        var createdRecipe = await _recipeService.CreateRecipeAsync(recipe);
        return CreatedAtAction(nameof(GetRecipe), new { id = createdRecipe.Id }, createdRecipe);
    }
    
    [HttpPut("{id}")]
    public async Task<IActionResult> UpdateRecipe(int id, Recipe recipe)
    {
        if (id != recipe.Id)
            return BadRequest();
            
        var updatedRecipe = await _recipeService.UpdateRecipeAsync(recipe);
        if (updatedRecipe == null)
            return NotFound();
            
        return NoContent();
    }
    
    [HttpDelete("{id}")]
    public async Task<IActionResult> DeleteRecipe(int id)
    {
        await _recipeService.DeleteRecipeAsync(id);
        return NoContent();
    }
    
    [HttpGet("search")]
    public async Task<ActionResult<IEnumerable<Recipe>>> SearchRecipes([FromQuery] string q)
    {
        if (string.IsNullOrWhiteSpace(q))
            return BadRequest("Search term is required");
            
        var recipes = await _recipeService.SearchRecipesAsync(q);
        return Ok(recipes);
    }
}
```

#### Web Application
```csharp
// RealWorldApp.Web/Controllers/RecipesController.cs
public class RecipesController : Controller
{
    private readonly IRecipeService _recipeService;
    
    public RecipesController(IRecipeService recipeService)
    {
        _recipeService = recipeService;
    }
    
    public async Task<IActionResult> Index()
    {
        var recipes = await _recipeService.GetAllRecipesAsync();
        return View(recipes);
    }
    
    public async Task<IActionResult> Details(int id)
    {
        var recipe = await _recipeService.GetRecipeByIdAsync(id);
        if (recipe == null)
            return NotFound();
            
        return View(recipe);
    }
    
    public IActionResult Create()
    {
        return View();
    }
    
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Create(Recipe recipe)
    {
        if (ModelState.IsValid)
        {
            await _recipeService.CreateRecipeAsync(recipe);
            return RedirectToAction(nameof(Index));
        }
        return View(recipe);
    }
    
    public async Task<IActionResult> Edit(int id)
    {
        var recipe = await _recipeService.GetRecipeByIdAsync(id);
        if (recipe == null)
            return NotFound();
            
        return View(recipe);
    }
    
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Edit(int id, Recipe recipe)
    {
        if (id != recipe.Id)
            return NotFound();
            
        if (ModelState.IsValid)
        {
            await _recipeService.UpdateRecipeAsync(recipe);
            return RedirectToAction(nameof(Index));
        }
        return View(recipe);
    }
    
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Delete(int id)
    {
        await _recipeService.DeleteRecipeAsync(id);
        return RedirectToAction(nameof(Index));
    }
}
```

#### Testing
```csharp
// RealWorldApp.Tests/RecipeServiceTests.cs
public class RecipeServiceTests
{
    private readonly Mock<ApplicationDbContext> _mockContext;
    private readonly RecipeService _recipeService;
    
    public RecipeServiceTests()
    {
        _mockContext = new Mock<ApplicationDbContext>();
        _recipeService = new RecipeService(_mockContext.Object);
    }
    
    [Fact]
    public async Task GetAllRecipesAsync_ShouldReturnAllRecipes()
    {
        // Arrange
        var recipes = new List<Recipe>
        {
            new Recipe { Id = 1, Title = "Recipe 1" },
            new Recipe { Id = 2, Title = "Recipe 2" }
        };
        
        var mockDbSet = recipes.AsQueryable().BuildMockDbSet();
        _mockContext.Setup(c => c.Recipes).Returns(mockDbSet.Object);
        
        // Act
        var result = await _recipeService.GetAllRecipesAsync();
        
        // Assert
        Assert.Equal(2, result.Count());
    }
    
    [Fact]
    public async Task GetRecipeByIdAsync_WithValidId_ShouldReturnRecipe()
    {
        // Arrange
        var recipe = new Recipe { Id = 1, Title = "Test Recipe" };
        var mockDbSet = new List<Recipe> { recipe }.AsQueryable().BuildMockDbSet();
        _mockContext.Setup(c => c.Recipes).Returns(mockDbSet.Object);
        
        // Act
        var result = await _recipeService.GetRecipeByIdAsync(1);
        
        // Assert
        Assert.NotNull(result);
        Assert.Equal("Test Recipe", result.Title);
    }
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Not planning the architecture
// Jumping straight into coding without design

// Not separating concerns
public class RecipeController : Controller
{
    private readonly ApplicationDbContext _context; // Direct database access in controller
    
    public async Task<IActionResult> Index()
    {
        var recipes = await _context.Recipes.ToListAsync(); // Business logic in controller
        return View(recipes);
    }
}

// Not handling exceptions properly
public async Task<IActionResult> Create(Recipe recipe)
{
    _context.Recipes.Add(recipe);
    await _context.SaveChangesAsync(); // No exception handling
    return RedirectToAction("Index");
}

// Not testing your code
// Skipping unit tests and integration tests
```

### ✅ Do This Instead:
```csharp
// Plan your architecture first
// Design your data models and API structure

// Separate concerns properly
public class RecipeController : Controller
{
    private readonly IRecipeService _recipeService; // Use service layer
    
    public async Task<IActionResult> Index()
    {
        var recipes = await _recipeService.GetAllRecipesAsync(); // Business logic in service
        return View(recipes);
    }
}

// Handle exceptions properly
public async Task<IActionResult> Create(Recipe recipe)
{
    try
    {
        await _recipeService.CreateRecipeAsync(recipe);
        return RedirectToAction("Index");
    }
    catch (Exception ex)
    {
        ModelState.AddModelError("", "Error creating recipe");
        return View(recipe);
    }
}

// Write comprehensive tests
// Test all your business logic and API endpoints
```

## Testing Your Application

### Test Cases to Try:
1. **Full CRUD operations**: Create, read, update, delete all entities
2. **API testing**: Test all API endpoints with Postman or similar
3. **Web interface**: Test all web pages and forms
4. **Database operations**: Verify data persistence and relationships
5. **Error handling**: Test error scenarios and edge cases
6. **Performance**: Test with larger datasets
7. **Security**: Test authentication and authorization
8. **Deployment**: Deploy to cloud platform

### How to Run:
```bash
# Run API
cd RealWorldApp.API
dotnet run

# Run Web Application
cd RealWorldApp.Web
dotnet run

# Run Tests
cd RealWorldApp.Tests
dotnet test
```

## Submission Checklist
- [ ] Complete application with all features implemented
- [ ] Database is properly designed and populated
- [ ] API endpoints work correctly
- [ ] Web interface is functional and user-friendly
- [ ] Authentication system is implemented
- [ ] Unit tests are written and passing
- [ ] Application is deployed to cloud platform
- [ ] Documentation is complete
- [ ] Code follows best practices and patterns
- [ ] Application is production-ready

## Bonus Challenges
1. **Real-time Features**: Add SignalR for live updates
2. **Advanced Search**: Implement full-text search
3. **File Upload**: Add image upload for recipes
4. **Mobile App**: Create mobile-responsive design
5. **Third-party Integrations**: Add external APIs
6. **Caching**: Implement Redis caching
7. **Monitoring**: Add application insights
8. **CI/CD**: Set up automated deployment pipeline

## Resources
- [ASP.NET Core Best Practices](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/)
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [Testing in .NET](https://docs.microsoft.com/en-us/dotnet/core/testing/)
- [Azure Deployment](https://docs.microsoft.com/en-us/azure/app-service/)

## Congratulations!
You've completed the comprehensive C# and .NET Core learning path! You now have the skills to build real-world applications and are ready to tackle professional development challenges. 