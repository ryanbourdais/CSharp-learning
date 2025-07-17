# Project 9: Library Management System

## Assignment Summary
Create a comprehensive library management system using ASP.NET Core MVC and Entity Framework Core. Manage books, authors, borrowers, and track book checkouts. This project introduces database operations, relationships, and complex business logic.

## Learning Objectives
- Set up Entity Framework Core with SQL Server or SQLite
- Create and manage database relationships
- Implement CRUD operations with database
- Work with complex data models
- Handle database migrations
- Practice real-world business logic

## Project Requirements

### Basic Requirements
1. **Database Setup**: Configure Entity Framework Core with database
2. **Book Management**: Add, edit, delete, and search books
3. **Author Management**: Manage author information
4. **Borrower Management**: Track library members
5. **Checkout System**: Check books in and out
6. **Reports**: Generate basic library reports

### Advanced Requirements (Optional)
- Add book categories/genres
- Implement due date tracking and fines
- Add book reservations
- Create advanced search functionality
- Add user authentication
- Generate detailed reports

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new mvc -n LibraryManagement
cd LibraryManagement
```

### Step 2: Install Required Packages
```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package Microsoft.EntityFrameworkCore.Design
```

### Step 3: Create Data Models
Create the following models:

```csharp
// Models/Author.cs
public class Author
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
    public DateTime BirthDate { get; set; }
    
    // Navigation property
    public ICollection<Book> Books { get; set; } = new List<Book>();
}

// Models/Book.cs
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string ISBN { get; set; }
    public int PublicationYear { get; set; }
    public int AuthorId { get; set; }
    public bool IsAvailable { get; set; } = true;
    
    // Navigation properties
    public Author Author { get; set; }
    public ICollection<Checkout> Checkouts { get; set; } = new List<Checkout>();
}

// Models/Borrower.cs
public class Borrower
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
    public string Phone { get; set; }
    public DateTime MembershipDate { get; set; }
    
    // Navigation property
    public ICollection<Checkout> Checkouts { get; set; } = new List<Checkout>();
}

// Models/Checkout.cs
public class Checkout
{
    public int Id { get; set; }
    public int BookId { get; set; }
    public int BorrowerId { get; set; }
    public DateTime CheckoutDate { get; set; }
    public DateTime? ReturnDate { get; set; }
    public DateTime DueDate { get; set; }
    
    // Navigation properties
    public Book Book { get; set; }
    public Borrower Borrower { get; set; }
}
```

## Hints and Code Examples

### Database Context
```csharp
// Data/LibraryContext.cs
using Microsoft.EntityFrameworkCore;

public class LibraryContext : DbContext
{
    public LibraryContext(DbContextOptions<LibraryContext> options) : base(options)
    {
    }
    
    public DbSet<Author> Authors { get; set; }
    public DbSet<Book> Books { get; set; }
    public DbSet<Borrower> Borrowers { get; set; }
    public DbSet<Checkout> Checkouts { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Configure relationships
        modelBuilder.Entity<Book>()
            .HasOne(b => b.Author)
            .WithMany(a => a.Books)
            .HasForeignKey(b => b.AuthorId);
            
        modelBuilder.Entity<Checkout>()
            .HasOne(c => c.Book)
            .WithMany(b => b.Checkouts)
            .HasForeignKey(c => c.BookId);
            
        modelBuilder.Entity<Checkout>()
            .HasOne(c => c.Borrower)
            .WithMany(b => b.Checkouts)
            .HasForeignKey(c => c.BorrowerId);
    }
}
```

### Update Program.cs
```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllersWithViews();

// Add DbContext
builder.Services.AddDbContext<LibraryContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

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

### Connection String
```json
// appsettings.json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=LibraryDB;Trusted_Connection=true;MultipleActiveResultSets=true"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### Book Controller
```csharp
// Controllers/BooksController.cs
public class BooksController : Controller
{
    private readonly LibraryContext _context;
    
    public BooksController(LibraryContext context)
    {
        _context = context;
    }
    
    public async Task<IActionResult> Index()
    {
        var books = await _context.Books
            .Include(b => b.Author)
            .ToListAsync();
        return View(books);
    }
    
    public async Task<IActionResult> Details(int? id)
    {
        if (id == null)
            return NotFound();
            
        var book = await _context.Books
            .Include(b => b.Author)
            .FirstOrDefaultAsync(b => b.Id == id);
            
        if (book == null)
            return NotFound();
            
        return View(book);
    }
    
    public IActionResult Create()
    {
        ViewBag.Authors = new SelectList(_context.Authors, "Id", "Name");
        return View();
    }
    
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Create([Bind("Title,ISBN,PublicationYear,AuthorId")] Book book)
    {
        if (ModelState.IsValid)
        {
            _context.Add(book);
            await _context.SaveChangesAsync();
            return RedirectToAction(nameof(Index));
        }
        ViewBag.Authors = new SelectList(_context.Authors, "Id", "Name");
        return View(book);
    }
    
    public async Task<IActionResult> Edit(int? id)
    {
        if (id == null)
            return NotFound();
            
        var book = await _context.Books.FindAsync(id);
        if (book == null)
            return NotFound();
            
        ViewBag.Authors = new SelectList(_context.Authors, "Id", "Name");
        return View(book);
    }
    
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Edit(int id, [Bind("Id,Title,ISBN,PublicationYear,AuthorId,IsAvailable")] Book book)
    {
        if (id != book.Id)
            return NotFound();
            
        if (ModelState.IsValid)
        {
            try
            {
                _context.Update(book);
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                if (!BookExists(book.Id))
                    return NotFound();
                else
                    throw;
            }
            return RedirectToAction(nameof(Index));
        }
        ViewBag.Authors = new SelectList(_context.Authors, "Id", "Name");
        return View(book);
    }
    
    private bool BookExists(int id)
    {
        return _context.Books.Any(e => e.Id == id);
    }
}
```

### Checkout Controller
```csharp
// Controllers/CheckoutsController.cs
public class CheckoutsController : Controller
{
    private readonly LibraryContext _context;
    
    public CheckoutsController(LibraryContext context)
    {
        _context = context;
    }
    
    public async Task<IActionResult> Index()
    {
        var checkouts = await _context.Checkouts
            .Include(c => c.Book)
            .Include(c => c.Borrower)
            .OrderByDescending(c => c.CheckoutDate)
            .ToListAsync();
        return View(checkouts);
    }
    
    public async Task<IActionResult> Checkout(int? bookId)
    {
        if (bookId == null)
            return NotFound();
            
        var book = await _context.Books
            .Include(b => b.Author)
            .FirstOrDefaultAsync(b => b.Id == bookId);
            
        if (book == null)
            return NotFound();
            
        if (!book.IsAvailable)
        {
            TempData["Message"] = "This book is not available for checkout.";
            return RedirectToAction("Index", "Books");
        }
        
        ViewBag.Borrowers = new SelectList(_context.Borrowers, "Id", "Name");
        ViewBag.Book = book;
        
        return View();
    }
    
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Checkout(int bookId, int borrowerId)
    {
        var book = await _context.Books.FindAsync(bookId);
        var borrower = await _context.Borrowers.FindAsync(borrowerId);
        
        if (book == null || borrower == null)
            return NotFound();
            
        if (!book.IsAvailable)
        {
            TempData["Message"] = "This book is not available for checkout.";
            return RedirectToAction("Index", "Books");
        }
        
        var checkout = new Checkout
        {
            BookId = bookId,
            BorrowerId = borrowerId,
            CheckoutDate = DateTime.Now,
            DueDate = DateTime.Now.AddDays(14) // 2 weeks
        };
        
        book.IsAvailable = false;
        
        _context.Checkouts.Add(checkout);
        await _context.SaveChangesAsync();
        
        TempData["Message"] = $"Book '{book.Title}' checked out successfully. Due date: {checkout.DueDate:MMM dd, yyyy}";
        return RedirectToAction("Index", "Books");
    }
    
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<IActionResult> Return(int checkoutId)
    {
        var checkout = await _context.Checkouts
            .Include(c => c.Book)
            .FirstOrDefaultAsync(c => c.Id == checkoutId);
            
        if (checkout == null)
            return NotFound();
            
        checkout.ReturnDate = DateTime.Now;
        checkout.Book.IsAvailable = true;
        
        await _context.SaveChangesAsync();
        
        TempData["Message"] = $"Book '{checkout.Book.Title}' returned successfully.";
        return RedirectToAction("Index");
    }
}
```

### Database Migration
```bash
# Create initial migration
dotnet ef migrations add InitialCreate

# Apply migration to database
dotnet ef database update
```

### Seed Data
```csharp
// Data/DbInitializer.cs
public static class DbInitializer
{
    public static void Initialize(LibraryContext context)
    {
        context.Database.EnsureCreated();
        
        // Check if data already exists
        if (context.Authors.Any())
            return;
            
        // Add authors
        var authors = new Author[]
        {
            new Author { Name = "J.K. Rowling", Biography = "British author", BirthDate = new DateTime(1965, 7, 31) },
            new Author { Name = "George R.R. Martin", Biography = "American author", BirthDate = new DateTime(1948, 9, 20) },
            new Author { Name = "Stephen King", Biography = "American author", BirthDate = new DateTime(1947, 9, 21) }
        };
        
        context.Authors.AddRange(authors);
        context.SaveChanges();
        
        // Add books
        var books = new Book[]
        {
            new Book { Title = "Harry Potter and the Philosopher's Stone", ISBN = "9780747532699", PublicationYear = 1997, AuthorId = authors[0].Id },
            new Book { Title = "A Game of Thrones", ISBN = "9780553103540", PublicationYear = 1996, AuthorId = authors[1].Id },
            new Book { Title = "The Shining", ISBN = "9780385121675", PublicationYear = 1977, AuthorId = authors[2].Id }
        };
        
        context.Books.AddRange(books);
        context.SaveChanges();
        
        // Add borrowers
        var borrowers = new Borrower[]
        {
            new Borrower { Name = "John Doe", Email = "john@example.com", Phone = "555-0101", MembershipDate = DateTime.Now.AddMonths(-6) },
            new Borrower { Name = "Jane Smith", Email = "jane@example.com", Phone = "555-0102", MembershipDate = DateTime.Now.AddMonths(-3) }
        };
        
        context.Borrowers.AddRange(borrowers);
        context.SaveChanges();
    }
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Not including related data
var books = await _context.Books.ToListAsync(); // Missing Author data

// Not handling database exceptions
_context.Add(book);
await _context.SaveChangesAsync(); // Could fail silently

// Not validating foreign keys
public async Task<IActionResult> Create(Book book)
{
    _context.Add(book); // No validation of AuthorId
    await _context.SaveChangesAsync();
}

// Not using async/await properly
public IActionResult Index()
{
    var books = _context.Books.ToList(); // Blocking call
    return View(books);
}
```

### ✅ Do This Instead:
```csharp
// Include related data
var books = await _context.Books
    .Include(b => b.Author)
    .ToListAsync();

// Handle exceptions
try
{
    _context.Add(book);
    await _context.SaveChangesAsync();
}
catch (DbUpdateException ex)
{
    ModelState.AddModelError("", "Error saving to database");
}

// Validate foreign keys
public async Task<IActionResult> Create(Book book)
{
    var author = await _context.Authors.FindAsync(book.AuthorId);
    if (author == null)
    {
        ModelState.AddModelError("AuthorId", "Invalid author");
    }
    
    if (ModelState.IsValid)
    {
        _context.Add(book);
        await _context.SaveChangesAsync();
    }
}

// Use async/await properly
public async Task<IActionResult> Index()
{
    var books = await _context.Books.ToListAsync();
    return View(books);
}
```

## Testing Your Application

### Test Cases to Try:
1. **Add books**: Create new books with authors
2. **Add borrowers**: Register new library members
3. **Checkout books**: Check out books to borrowers
4. **Return books**: Return checked out books
5. **Search functionality**: Search books by title or author
6. **Reports**: Generate checkout reports
7. **Validation**: Try invalid data → Should show errors

### How to Run:
```bash
dotnet run
```
Then open `https://localhost:5001` in your browser.

## Submission Checklist
- [ ] Project compiles without errors
- [ ] Database is created and seeded with data
- [ ] Books can be added, edited, and deleted
- [ ] Authors can be managed
- [ ] Borrowers can be registered
- [ ] Books can be checked out and returned
- [ ] Relationships work correctly
- [ ] Basic reports are generated
- [ ] Error handling is implemented
- [ ] Code follows Entity Framework best practices

## Bonus Challenges
1. **Due Date Tracking**: Implement overdue book notifications
2. **Fines System**: Calculate fines for overdue books
3. **Book Reservations**: Allow reserving unavailable books
4. **Advanced Search**: Search by multiple criteria
5. **User Authentication**: Add login system for librarians
6. **Book Categories**: Add genres and categories
7. **Email Notifications**: Send due date reminders
8. **Statistics Dashboard**: Show library usage statistics

## Resources
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [ASP.NET Core with EF Core](https://docs.microsoft.com/en-us/aspnet/core/data/ef-mvc/)
- [Database Migrations](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/)
- [LINQ to Entities](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/ef/language-reference/linq-to-entities)

## Next Steps
Once you complete this project, you'll be ready for Project 10: Personal Finance Tracker, where you'll learn about complex data relationships and advanced LINQ operations. 