# Project 8: Guest Book

## Assignment Summary
Build a web application where visitors can leave messages, including their name and a comment. This project introduces form handling, model binding, data validation, and basic data persistence in ASP.NET Core MVC.

## Learning Objectives
- Handle HTML forms and form submission
- Implement model binding and validation
- Work with data models and view models
- Store and retrieve data (in-memory or file-based)
- Implement basic CRUD operations in web context
- Practice web security basics

## Project Requirements

### Basic Requirements
1. **Display Messages**: Show all guest book entries
2. **Add Message Form**: Form to add new guest book entries
3. **Data Model**: Create a model for guest book entries
4. **Form Validation**: Validate user input
5. **Data Persistence**: Store messages (in-memory or file)
6. **Display Format**: Show messages in a readable format

### Advanced Requirements (Optional)
- Add message timestamps
- Implement message editing/deletion
- Add user authentication
- Add message categories
- Implement search functionality
- Add pagination for many messages

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new mvc -n GuestBook
cd GuestBook
```

### Step 2: Create the Data Model
Create a model for guest book entries:

```csharp
// Models/GuestBookEntry.cs
public class GuestBookEntry
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Message { get; set; }
    public DateTime CreatedDate { get; set; }
    
    public GuestBookEntry()
    {
        CreatedDate = DateTime.Now;
    }
}
```

### Step 3: Create a Service for Data Management
```csharp
// Services/IGuestBookService.cs
public interface IGuestBookService
{
    List<GuestBookEntry> GetAllEntries();
    void AddEntry(GuestBookEntry entry);
    GuestBookEntry GetEntry(int id);
    void DeleteEntry(int id);
}

// Services/GuestBookService.cs
public class GuestBookService : IGuestBookService
{
    private List<GuestBookEntry> _entries = new List<GuestBookEntry>();
    private int _nextId = 1;
    
    public List<GuestBookEntry> GetAllEntries()
    {
        return _entries.OrderByDescending(e => e.CreatedDate).ToList();
    }
    
    public void AddEntry(GuestBookEntry entry)
    {
        entry.Id = _nextId++;
        entry.CreatedDate = DateTime.Now;
        _entries.Add(entry);
    }
    
    public GuestBookEntry GetEntry(int id)
    {
        return _entries.FirstOrDefault(e => e.Id == id);
    }
    
    public void DeleteEntry(int id)
    {
        var entry = _entries.FirstOrDefault(e => e.Id == id);
        if (entry != null)
        {
            _entries.Remove(entry);
        }
    }
}
```

## Hints and Code Examples

### Controller Implementation
```csharp
// Controllers/GuestBookController.cs
public class GuestBookController : Controller
{
    private readonly IGuestBookService _guestBookService;
    
    public GuestBookController(IGuestBookService guestBookService)
    {
        _guestBookService = guestBookService;
    }
    
    public IActionResult Index()
    {
        var entries = _guestBookService.GetAllEntries();
        return View(entries);
    }
    
    public IActionResult Create()
    {
        return View();
    }
    
    [HttpPost]
    public IActionResult Create(GuestBookEntry entry)
    {
        if (ModelState.IsValid)
        {
            _guestBookService.AddEntry(entry);
            return RedirectToAction(nameof(Index));
        }
        return View(entry);
    }
    
    [HttpPost]
    public IActionResult Delete(int id)
    {
        _guestBookService.DeleteEntry(id);
        return RedirectToAction(nameof(Index));
    }
}
```

### Main View (Views/GuestBook/Index.cshtml)
```html
@model IEnumerable<GuestBookEntry>

@{
    ViewData["Title"] = "Guest Book";
}

<div class="row">
    <div class="col-md-8">
        <h1>Guest Book</h1>
        <p class="lead">Leave a message for others to see!</p>
        
        @if (Model.Any())
        {
            foreach (var entry in Model)
            {
                <div class="card mb-3">
                    <div class="card-body">
                        <div class="d-flex justify-content-between">
                            <h5 class="card-title">@entry.Name</h5>
                            <small class="text-muted">@entry.CreatedDate.ToString("MMM dd, yyyy 'at' HH:mm")</small>
                        </div>
                        <p class="card-text">@entry.Message</p>
                        <form asp-action="Delete" method="post" style="display: inline;">
                            <input type="hidden" name="id" value="@entry.Id" />
                            <button type="submit" class="btn btn-danger btn-sm" 
                                    onclick="return confirm('Are you sure you want to delete this message?')">
                                Delete
                            </button>
                        </form>
                    </div>
                </div>
            }
        }
        else
        {
            <div class="alert alert-info">
                No messages yet. Be the first to leave a message!
            </div>
        }
    </div>
    
    <div class="col-md-4">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Leave a Message</h5>
                <a asp-action="Create" class="btn btn-primary">Add New Message</a>
            </div>
        </div>
    </div>
</div>
```

### Create Form View (Views/GuestBook/Create.cshtml)
```html
@model GuestBookEntry

@{
    ViewData["Title"] = "Add Message";
}

<div class="row">
    <div class="col-md-8 offset-md-2">
        <h1>Leave a Message</h1>
        
        <form asp-action="Create" method="post">
            <div class="form-group">
                <label asp-for="Name" class="control-label">Your Name</label>
                <input asp-for="Name" class="form-control" />
                <span asp-validation-for="Name" class="text-danger"></span>
            </div>
            
            <div class="form-group">
                <label asp-for="Message" class="control-label">Message</label>
                <textarea asp-for="Message" class="form-control" rows="5"></textarea>
                <span asp-validation-for="Message" class="text-danger"></span>
            </div>
            
            <div class="form-group">
                <button type="submit" class="btn btn-primary">Post Message</button>
                <a asp-action="Index" class="btn btn-secondary">Cancel</a>
            </div>
        </form>
    </div>
</div>

@section Scripts {
    @{await Html.RenderPartialAsync("_ValidationScriptsPartial");}
}
```

### Update Program.cs for Dependency Injection
```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllersWithViews();
builder.Services.AddSingleton<IGuestBookService, GuestBookService>();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseRouting();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();
```

### Add Validation to Model
```csharp
// Models/GuestBookEntry.cs
using System.ComponentModel.DataAnnotations;

public class GuestBookEntry
{
    public int Id { get; set; }
    
    [Required(ErrorMessage = "Name is required")]
    [StringLength(50, ErrorMessage = "Name cannot be longer than 50 characters")]
    public string Name { get; set; }
    
    [Required(ErrorMessage = "Message is required")]
    [StringLength(500, ErrorMessage = "Message cannot be longer than 500 characters")]
    public string Message { get; set; }
    
    public DateTime CreatedDate { get; set; }
    
    public GuestBookEntry()
    {
        CreatedDate = DateTime.Now;
    }
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Not using dependency injection
public class GuestBookController : Controller
{
    private GuestBookService _service = new GuestBookService(); // Wrong!
}

// Not validating input
[HttpPost]
public IActionResult Create(GuestBookEntry entry)
{
    _service.AddEntry(entry); // No validation!
    return RedirectToAction("Index");
}

// Not using proper model binding
public IActionResult Create(string name, string message) // Should use model
{
    // Manual binding
}
```

### ✅ Do This Instead:
```csharp
// Use dependency injection
public class GuestBookController : Controller
{
    private readonly IGuestBookService _guestBookService;
    
    public GuestBookController(IGuestBookService guestBookService)
    {
        _guestBookService = guestBookService;
    }
}

// Validate input
[HttpPost]
public IActionResult Create(GuestBookEntry entry)
{
    if (ModelState.IsValid)
    {
        _guestBookService.AddEntry(entry);
        return RedirectToAction(nameof(Index));
    }
    return View(entry);
}

// Use proper model binding
public IActionResult Create(GuestBookEntry entry) // Model binding handles it
{
    // Automatic binding
}
```

## Testing Your Application

### Test Cases to Try:
1. **Add message**: Fill out form and submit → Should add to list
2. **Validation**: Try to submit empty form → Should show validation errors
3. **View messages**: Check if messages display correctly
4. **Delete message**: Click delete button → Should remove message
5. **Long input**: Try very long name/message → Should show validation error
6. **Special characters**: Use HTML in message → Should be displayed safely

### How to Run:
```bash
dotnet run
```
Then open `https://localhost:5001/GuestBook` in your browser.

## Submission Checklist
- [ ] Project compiles without errors
- [ ] Guest book displays all messages
- [ ] Add message form works correctly
- [ ] Form validation works properly
- [ ] Messages are stored and retrieved
- [ ] Delete functionality works
- [ ] Messages display with timestamps
- [ ] Input is properly validated
- [ ] Code follows MVC pattern
- [ ] Application is user-friendly

## Bonus Challenges
1. **File Persistence**: Save messages to a JSON file
2. **Message Editing**: Allow editing existing messages
3. **User Authentication**: Add login system
4. **Message Categories**: Add categories/tags to messages
5. **Search Function**: Search through messages
6. **Pagination**: Handle many messages with pagination
7. **Rich Text**: Allow basic formatting in messages
8. **Email Notifications**: Send email when new message is posted

## Resources
- [ASP.NET Core Model Binding](https://docs.microsoft.com/en-us/aspnet/core/mvc/models/model-binding)
- [Data Annotations](https://docs.microsoft.com/en-us/aspnet/core/mvc/models/validation)
- [Dependency Injection](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection)
- [Form Handling](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/working-with-forms)

## Next Steps
Once you complete this project, you'll be ready for Project 9: Library Management System, where you'll learn about Entity Framework Core and database operations. 