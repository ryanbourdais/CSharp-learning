# Project 10: Personal Finance Tracker

## Assignment Summary
Build a comprehensive personal finance tracking application that manages income, expenses, savings goals, and provides financial insights. This project introduces complex data relationships, advanced LINQ queries, data visualization, and sophisticated business logic.

## Learning Objectives
- Work with complex data relationships and models
- Implement advanced LINQ queries and aggregations
- Create data visualization and reporting
- Handle complex business logic and calculations
- Practice data analysis and insights generation
- Implement user authentication basics

## Project Requirements

### Basic Requirements
1. **Income Tracking**: Record and categorize income sources
2. **Expense Management**: Track and categorize expenses
3. **Savings Goals**: Set and monitor savings targets
4. **Financial Reports**: Generate monthly/yearly reports
5. **Data Visualization**: Create charts and graphs
6. **Budget Planning**: Set and track budget limits

### Advanced Requirements (Optional)
- Add investment tracking
- Implement debt management
- Create financial forecasting
- Add recurring transactions
- Implement data export/import
- Add mobile-responsive design

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new mvc -n PersonalFinanceTracker
cd PersonalFinanceTracker
```

### Step 2: Install Required Packages
```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Chart.js
```

### Step 3: Create Complex Data Models
```csharp
// Models/User.cs
public class User
{
    public int Id { get; set; }
    public string Username { get; set; }
    public string Email { get; set; }
    public DateTime CreatedDate { get; set; }
    
    // Navigation properties
    public ICollection<Account> Accounts { get; set; } = new List<Account>();
    public ICollection<Category> Categories { get; set; } = new List<Category>();
    public ICollection<SavingsGoal> SavingsGoals { get; set; } = new List<SavingsGoal>();
}

// Models/Account.cs
public class Account
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Type { get; set; } // Checking, Savings, Credit, etc.
    public decimal Balance { get; set; }
    public int UserId { get; set; }
    
    // Navigation properties
    public User User { get; set; }
    public ICollection<Transaction> Transactions { get; set; } = new List<Transaction>();
}

// Models/Category.cs
public class Category
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Type { get; set; } // Income, Expense
    public string Color { get; set; }
    public int UserId { get; set; }
    
    // Navigation properties
    public User User { get; set; }
    public ICollection<Transaction> Transactions { get; set; } = new List<Transaction>();
}

// Models/Transaction.cs
public class Transaction
{
    public int Id { get; set; }
    public string Description { get; set; }
    public decimal Amount { get; set; }
    public DateTime Date { get; set; }
    public string Type { get; set; } // Income, Expense
    public int AccountId { get; set; }
    public int CategoryId { get; set; }
    public int UserId { get; set; }
    public bool IsRecurring { get; set; }
    public string? RecurringPattern { get; set; } // Monthly, Weekly, etc.
    
    // Navigation properties
    public Account Account { get; set; }
    public Category Category { get; set; }
    public User User { get; set; }
}

// Models/SavingsGoal.cs
public class SavingsGoal
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public decimal TargetAmount { get; set; }
    public decimal CurrentAmount { get; set; }
    public DateTime TargetDate { get; set; }
    public int UserId { get; set; }
    
    // Navigation properties
    public User User { get; set; }
}

// Models/Budget.cs
public class Budget
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Amount { get; set; }
    public string Period { get; set; } // Monthly, Yearly
    public int CategoryId { get; set; }
    public int UserId { get; set; }
    public DateTime StartDate { get; set; }
    
    // Navigation properties
    public Category Category { get; set; }
    public User User { get; set; }
}
```

## Hints and Code Examples

### Database Context with Complex Relationships
```csharp
// Data/FinanceContext.cs
public class FinanceContext : DbContext
{
    public FinanceContext(DbContextOptions<FinanceContext> options) : base(options)
    {
    }
    
    public DbSet<User> Users { get; set; }
    public DbSet<Account> Accounts { get; set; }
    public DbSet<Category> Categories { get; set; }
    public DbSet<Transaction> Transactions { get; set; }
    public DbSet<SavingsGoal> SavingsGoals { get; set; }
    public DbSet<Budget> Budgets { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Configure relationships
        modelBuilder.Entity<Account>()
            .HasOne(a => a.User)
            .WithMany(u => u.Accounts)
            .HasForeignKey(a => a.UserId);
            
        modelBuilder.Entity<Category>()
            .HasOne(c => c.User)
            .WithMany(u => u.Categories)
            .HasForeignKey(c => c.UserId);
            
        modelBuilder.Entity<Transaction>()
            .HasOne(t => t.User)
            .WithMany()
            .HasForeignKey(t => t.UserId);
            
        modelBuilder.Entity<Transaction>()
            .HasOne(t => t.Account)
            .WithMany(a => a.Transactions)
            .HasForeignKey(t => t.AccountId);
            
        modelBuilder.Entity<Transaction>()
            .HasOne(t => t.Category)
            .WithMany(c => c.Transactions)
            .HasForeignKey(t => t.CategoryId);
            
        modelBuilder.Entity<SavingsGoal>()
            .HasOne(s => s.User)
            .WithMany(u => u.SavingsGoals)
            .HasForeignKey(s => s.UserId);
            
        modelBuilder.Entity<Budget>()
            .HasOne(b => b.User)
            .WithMany()
            .HasForeignKey(b => b.UserId);
            
        modelBuilder.Entity<Budget>()
            .HasOne(b => b.Category)
            .WithMany()
            .HasForeignKey(b => b.CategoryId);
    }
}
```

### Financial Analysis Service
```csharp
// Services/IFinancialAnalysisService.cs
public interface IFinancialAnalysisService
{
    Task<FinancialSummary> GetFinancialSummaryAsync(int userId, DateTime startDate, DateTime endDate);
    Task<List<CategorySummary>> GetCategorySummaryAsync(int userId, DateTime startDate, DateTime endDate);
    Task<List<MonthlySummary>> GetMonthlySummaryAsync(int userId, int year);
    Task<BudgetAnalysis> GetBudgetAnalysisAsync(int userId, DateTime startDate, DateTime endDate);
    Task<List<SavingsGoalProgress>> GetSavingsGoalProgressAsync(int userId);
}

// Services/FinancialAnalysisService.cs
public class FinancialAnalysisService : IFinancialAnalysisService
{
    private readonly FinanceContext _context;
    
    public FinancialAnalysisService(FinanceContext context)
    {
        _context = context;
    }
    
    public async Task<FinancialSummary> GetFinancialSummaryAsync(int userId, DateTime startDate, DateTime endDate)
    {
        var transactions = await _context.Transactions
            .Where(t => t.UserId == userId && t.Date >= startDate && t.Date <= endDate)
            .ToListAsync();
            
        var income = transactions.Where(t => t.Type == "Income").Sum(t => t.Amount);
        var expenses = transactions.Where(t => t.Type == "Expense").Sum(t => t.Amount);
        var netIncome = income - expenses;
        
        return new FinancialSummary
        {
            TotalIncome = income,
            TotalExpenses = expenses,
            NetIncome = netIncome,
            TransactionCount = transactions.Count,
            Period = $"{startDate:MMM yyyy} - {endDate:MMM yyyy}"
        };
    }
    
    public async Task<List<CategorySummary>> GetCategorySummaryAsync(int userId, DateTime startDate, DateTime endDate)
    {
        var categorySummaries = await _context.Transactions
            .Where(t => t.UserId == userId && t.Date >= startDate && t.Date <= endDate)
            .GroupBy(t => new { t.CategoryId, t.Category.Name, t.Category.Color, t.Type })
            .Select(g => new CategorySummary
            {
                CategoryName = g.Key.Name,
                CategoryColor = g.Key.Color,
                Type = g.Key.Type,
                TotalAmount = g.Sum(t => t.Amount),
                TransactionCount = g.Count(),
                Percentage = 0 // Will be calculated later
            })
            .ToListAsync();
            
        // Calculate percentages
        var totalIncome = categorySummaries.Where(c => c.Type == "Income").Sum(c => c.TotalAmount);
        var totalExpenses = categorySummaries.Where(c => c.Type == "Expense").Sum(c => c.TotalAmount);
        
        foreach (var summary in categorySummaries)
        {
            var total = summary.Type == "Income" ? totalIncome : totalExpenses;
            summary.Percentage = total > 0 ? (summary.TotalAmount / total) * 100 : 0;
        }
        
        return categorySummaries;
    }
    
    public async Task<List<MonthlySummary>> GetMonthlySummaryAsync(int userId, int year)
    {
        var monthlySummaries = await _context.Transactions
            .Where(t => t.UserId == userId && t.Date.Year == year)
            .GroupBy(t => new { t.Date.Year, t.Date.Month, t.Type })
            .Select(g => new MonthlySummary
            {
                Year = g.Key.Year,
                Month = g.Key.Month,
                Type = g.Key.Type,
                TotalAmount = g.Sum(t => t.Amount),
                TransactionCount = g.Count()
            })
            .ToListAsync();
            
        return monthlySummaries;
    }
    
    public async Task<BudgetAnalysis> GetBudgetAnalysisAsync(int userId, DateTime startDate, DateTime endDate)
    {
        var budgets = await _context.Budgets
            .Include(b => b.Category)
            .Where(b => b.UserId == userId && b.StartDate <= endDate)
            .ToListAsync();
            
        var actualExpenses = await _context.Transactions
            .Where(t => t.UserId == userId && t.Type == "Expense" && t.Date >= startDate && t.Date <= endDate)
            .GroupBy(t => t.CategoryId)
            .Select(g => new { CategoryId = g.Key, Amount = g.Sum(t => t.Amount) })
            .ToListAsync();
            
        var budgetAnalysis = new List<BudgetItemAnalysis>();
        
        foreach (var budget in budgets)
        {
            var actualAmount = actualExpenses.FirstOrDefault(e => e.CategoryId == budget.CategoryId)?.Amount ?? 0;
            var percentageUsed = budget.Amount > 0 ? (actualAmount / budget.Amount) * 100 : 0;
            
            budgetAnalysis.Add(new BudgetItemAnalysis
            {
                CategoryName = budget.Category.Name,
                BudgetAmount = budget.Amount,
                ActualAmount = actualAmount,
                RemainingAmount = budget.Amount - actualAmount,
                PercentageUsed = percentageUsed,
                IsOverBudget = actualAmount > budget.Amount
            });
        }
        
        return new BudgetAnalysis
        {
            BudgetItems = budgetAnalysis,
            TotalBudget = budgets.Sum(b => b.Amount),
            TotalSpent = actualExpenses.Sum(e => e.Amount),
            TotalRemaining = budgets.Sum(b => b.Amount) - actualExpenses.Sum(e => e.Amount)
        };
    }
    
    public async Task<List<SavingsGoalProgress>> GetSavingsGoalProgressAsync(int userId)
    {
        var goals = await _context.SavingsGoals
            .Where(s => s.UserId == userId)
            .ToListAsync();
            
        var progress = goals.Select(g => new SavingsGoalProgress
        {
            GoalName = g.Name,
            TargetAmount = g.TargetAmount,
            CurrentAmount = g.CurrentAmount,
            ProgressPercentage = g.TargetAmount > 0 ? (g.CurrentAmount / g.TargetAmount) * 100 : 0,
            RemainingAmount = g.TargetAmount - g.CurrentAmount,
            DaysRemaining = (g.TargetDate - DateTime.Now).Days,
            IsOnTrack = g.CurrentAmount >= (g.TargetAmount * (DateTime.Now - g.TargetDate.AddYears(-1)).Days / 365)
        }).ToList();
        
        return progress;
    }
}
```

### Dashboard Controller
```csharp
// Controllers/DashboardController.cs
public class DashboardController : Controller
{
    private readonly FinanceContext _context;
    private readonly IFinancialAnalysisService _analysisService;
    
    public DashboardController(FinanceContext context, IFinancialAnalysisService analysisService)
    {
        _context = context;
        _analysisService = analysisService;
    }
    
    public async Task<IActionResult> Index()
    {
        // For demo purposes, use user ID 1
        int userId = 1;
        
        var startDate = DateTime.Now.AddMonths(-1);
        var endDate = DateTime.Now;
        
        var financialSummary = await _analysisService.GetFinancialSummaryAsync(userId, startDate, endDate);
        var categorySummary = await _analysisService.GetCategorySummaryAsync(userId, startDate, endDate);
        var monthlySummary = await _analysisService.GetMonthlySummaryAsync(userId, DateTime.Now.Year);
        var budgetAnalysis = await _analysisService.GetBudgetAnalysisAsync(userId, startDate, endDate);
        var savingsProgress = await _analysisService.GetSavingsGoalProgressAsync(userId);
        
        var dashboardViewModel = new DashboardViewModel
        {
            FinancialSummary = financialSummary,
            CategorySummary = categorySummary,
            MonthlySummary = monthlySummary,
            BudgetAnalysis = budgetAnalysis,
            SavingsProgress = savingsProgress
        };
        
        return View(dashboardViewModel);
    }
}
```

### Dashboard View with Charts
```html
<!-- Views/Dashboard/Index.cshtml -->
@model DashboardViewModel

@{
    ViewData["Title"] = "Financial Dashboard";
}

<div class="row">
    <div class="col-md-3">
        <div class="card bg-primary text-white">
            <div class="card-body">
                <h5 class="card-title">Total Income</h5>
                <h3>@Model.FinancialSummary.TotalIncome.ToString("C")</h3>
            </div>
        </div>
    </div>
    <div class="col-md-3">
        <div class="card bg-danger text-white">
            <div class="card-body">
                <h5 class="card-title">Total Expenses</h5>
                <h3>@Model.FinancialSummary.TotalExpenses.ToString("C")</h3>
            </div>
        </div>
    </div>
    <div class="col-md-3">
        <div class="card bg-success text-white">
            <div class="card-body">
                <h5 class="card-title">Net Income</h5>
                <h3>@Model.FinancialSummary.NetIncome.ToString("C")</h3>
            </div>
        </div>
    </div>
    <div class="col-md-3">
        <div class="card bg-info text-white">
            <div class="card-body">
                <h5 class="card-title">Transactions</h5>
                <h3>@Model.FinancialSummary.TransactionCount</h3>
            </div>
        </div>
    </div>
</div>

<div class="row mt-4">
    <div class="col-md-6">
        <div class="card">
            <div class="card-header">
                <h5>Expense Categories</h5>
            </div>
            <div class="card-body">
                <canvas id="expenseChart"></canvas>
            </div>
        </div>
    </div>
    <div class="col-md-6">
        <div class="card">
            <div class="card-header">
                <h5>Monthly Overview</h5>
            </div>
            <div class="card-body">
                <canvas id="monthlyChart"></canvas>
            </div>
        </div>
    </div>
</div>

<div class="row mt-4">
    <div class="col-md-12">
        <div class="card">
            <div class="card-header">
                <h5>Budget Analysis</h5>
            </div>
            <div class="card-body">
                <div class="table-responsive">
                    <table class="table">
                        <thead>
                            <tr>
                                <th>Category</th>
                                <th>Budget</th>
                                <th>Spent</th>
                                <th>Remaining</th>
                                <th>Progress</th>
                            </tr>
                        </thead>
                        <tbody>
                            @foreach (var item in Model.BudgetAnalysis.BudgetItems)
                            {
                                <tr class="@(item.IsOverBudget ? "table-danger" : "")">
                                    <td>@item.CategoryName</td>
                                    <td>@item.BudgetAmount.ToString("C")</td>
                                    <td>@item.ActualAmount.ToString("C")</td>
                                    <td>@item.RemainingAmount.ToString("C")</td>
                                    <td>
                                        <div class="progress">
                                            <div class="progress-bar @(item.IsOverBudget ? "bg-danger" : "bg-success")" 
                                                 style="width: @(Math.Min(item.PercentageUsed, 100))%">
                                                @item.PercentageUsed.ToString("F1")%
                                            </div>
                                        </div>
                                    </td>
                                </tr>
                            }
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>

@section Scripts {
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script>
        // Expense Chart
        const expenseCtx = document.getElementById('expenseChart').getContext('2d');
        new Chart(expenseCtx, {
            type: 'doughnut',
            data: {
                labels: @Json.Serialize(Model.CategorySummary.Where(c => c.Type == "Expense").Select(c => c.CategoryName)),
                datasets: [{
                    data: @Json.Serialize(Model.CategorySummary.Where(c => c.Type == "Expense").Select(c => c.TotalAmount)),
                    backgroundColor: @Json.Serialize(Model.CategorySummary.Where(c => c.Type == "Expense").Select(c => c.CategoryColor))
                }]
            },
            options: {
                responsive: true,
                plugins: {
                    legend: {
                        position: 'bottom'
                    }
                }
            }
        });
        
        // Monthly Chart
        const monthlyCtx = document.getElementById('monthlyChart').getContext('2d');
        new Chart(monthlyCtx, {
            type: 'line',
            data: {
                labels: @Json.Serialize(Model.MonthlySummary.Select(m => $"{m.Year}-{m.Month:D2}")),
                datasets: [{
                    label: 'Income',
                    data: @Json.Serialize(Model.MonthlySummary.Where(m => m.Type == "Income").Select(m => m.TotalAmount)),
                    borderColor: 'rgb(75, 192, 192)',
                    backgroundColor: 'rgba(75, 192, 192, 0.2)'
                }, {
                    label: 'Expenses',
                    data: @Json.Serialize(Model.MonthlySummary.Where(m => m.Type == "Expense").Select(m => m.TotalAmount)),
                    borderColor: 'rgb(255, 99, 132)',
                    backgroundColor: 'rgba(255, 99, 132, 0.2)'
                }]
            },
            options: {
                responsive: true,
                scales: {
                    y: {
                        beginAtZero: true
                    }
                }
            }
        });
    </script>
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Not using proper LINQ for complex queries
var transactions = await _context.Transactions.ToListAsync();
var income = transactions.Where(t => t.Type == "Income").Sum(t => t.Amount); // Inefficient

// Not handling null values in calculations
decimal percentage = (amount / total) * 100; // Could divide by zero

// Not using proper grouping
var summary = transactions.GroupBy(t => t.CategoryId)
    .Select(g => new { CategoryId = g.Key, Amount = g.Sum(t => t.Amount) }); // Missing navigation properties

// Not validating complex calculations
public decimal CalculateNetIncome(decimal income, decimal expenses)
{
    return income - expenses; // No validation
}
```

### ✅ Do This Instead:
```csharp
// Use efficient LINQ queries
var income = await _context.Transactions
    .Where(t => t.Type == "Income")
    .SumAsync(t => t.Amount);

// Handle null values and edge cases
decimal percentage = total > 0 ? (amount / total) * 100 : 0;

// Use proper grouping with navigation properties
var summary = await _context.Transactions
    .Include(t => t.Category)
    .GroupBy(t => new { t.CategoryId, t.Category.Name })
    .Select(g => new { CategoryName = g.Key.Name, Amount = g.Sum(t => t.Amount) })
    .ToListAsync();

// Validate calculations
public decimal CalculateNetIncome(decimal income, decimal expenses)
{
    if (income < 0 || expenses < 0)
        throw new ArgumentException("Income and expenses must be non-negative");
    return income - expenses;
}
```

## Testing Your Application

### Test Cases to Try:
1. **Add transactions**: Create income and expense transactions
2. **Category management**: Create and manage categories
3. **Budget tracking**: Set budgets and monitor spending
4. **Savings goals**: Create and track savings goals
5. **Reports generation**: Generate monthly/yearly reports
6. **Data visualization**: Check charts and graphs
7. **Complex queries**: Test advanced LINQ operations

### How to Run:
```bash
dotnet run
```
Then open `https://localhost:5001/Dashboard` in your browser.

## Submission Checklist
- [ ] Project compiles without errors
- [ ] Database is created with all tables
- [ ] Complex data relationships work correctly
- [ ] Advanced LINQ queries function properly
- [ ] Financial calculations are accurate
- [ ] Data visualization displays correctly
- [ ] Budget tracking works
- [ ] Savings goals are tracked
- [ ] Reports are generated
- [ ] Code follows best practices

## Bonus Challenges
1. **Investment Tracking**: Add investment portfolio management
2. **Debt Management**: Track loans and debt payments
3. **Financial Forecasting**: Predict future income/expenses
4. **Data Export**: Export data to Excel/CSV
5. **Mobile App**: Create mobile-responsive design
6. **Multi-currency**: Support multiple currencies
7. **Tax Tracking**: Track tax-deductible expenses
8. **Financial Goals**: Set and track financial goals

## Resources
- [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- [LINQ](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/)
- [Chart.js](https://www.chartjs.org/)
- [ASP.NET Core MVC](https://docs.microsoft.com/en-us/aspnet/core/mvc/)

## Next Steps
Once you complete this project, you'll be ready for Project 11: API Development, where you'll learn about REST APIs, JSON serialization, and API testing. 