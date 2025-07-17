# Project 5: Simple Bank Account

## Assignment Summary
Create a bank account class with basic banking functionality including deposits, withdrawals, and balance checking. This project introduces object-oriented programming concepts like classes, properties, methods, and encapsulation.

## Learning Objectives
- Create and use classes and objects
- Implement properties and methods
- Understand encapsulation and data hiding
- Work with constructors
- Handle basic business logic
- Practice object-oriented design

## Project Requirements

### Basic Requirements
1. **BankAccount Class**: Create a class to represent a bank account
2. **Properties**: Include account number, owner name, and balance
3. **Constructor**: Initialize account with owner name and starting balance
4. **Deposit Method**: Add money to the account
5. **Withdraw Method**: Remove money from the account (with validation)
6. **GetBalance Method**: Return current balance
7. **Display Account Info**: Show account details

### Advanced Requirements (Optional)
- Add transaction history
- Implement overdraft protection
- Add interest calculation
- Support multiple account types
- Add account transfer functionality
- Implement file persistence

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new console -n SimpleBankAccount
cd SimpleBankAccount
```

### Step 2: Design Your BankAccount Class
Start with the basic structure:

```csharp
public class BankAccount
{
    // Properties
    public string AccountNumber { get; private set; }
    public string OwnerName { get; set; }
    public decimal Balance { get; private set; }
    
    // Constructor
    public BankAccount(string ownerName, decimal initialBalance)
    {
        OwnerName = ownerName;
        Balance = initialBalance;
        AccountNumber = GenerateAccountNumber();
    }
    
    // Methods
    public void Deposit(decimal amount)
    {
        // Implementation
    }
    
    public bool Withdraw(decimal amount)
    {
        // Implementation
    }
    
    public decimal GetBalance()
    {
        // Implementation
    }
    
    private string GenerateAccountNumber()
    {
        // Implementation
    }
}
```

## Hints and Code Examples

### Basic BankAccount Class
```csharp
public class BankAccount
{
    public string AccountNumber { get; private set; }
    public string OwnerName { get; set; }
    public decimal Balance { get; private set; }
    
    public BankAccount(string ownerName, decimal initialBalance)
    {
        if (string.IsNullOrWhiteSpace(ownerName))
            throw new ArgumentException("Owner name cannot be empty");
            
        if (initialBalance < 0)
            throw new ArgumentException("Initial balance cannot be negative");
            
        OwnerName = ownerName;
        Balance = initialBalance;
        AccountNumber = GenerateAccountNumber();
    }
    
    public void Deposit(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Deposit amount must be positive");
            
        Balance += amount;
    }
    
    public bool Withdraw(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Withdrawal amount must be positive");
            
        if (amount > Balance)
        {
            return false; // Insufficient funds
        }
        
        Balance -= amount;
        return true;
    }
    
    public decimal GetBalance()
    {
        return Balance;
    }
    
    private string GenerateAccountNumber()
    {
        Random random = new Random();
        return $"ACC{random.Next(100000, 999999)}";
    }
    
    public void DisplayAccountInfo()
    {
        Console.WriteLine($"Account Number: {AccountNumber}");
        Console.WriteLine($"Owner: {OwnerName}");
        Console.WriteLine($"Balance: ${Balance:F2}");
    }
}
```

### Main Program to Test the Class
```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("=== Simple Bank Account System ===");
        
        try
        {
            // Create a new account
            Console.Write("Enter account owner name: ");
            string ownerName = Console.ReadLine();
            
            Console.Write("Enter initial balance: $");
            decimal initialBalance = Convert.ToDecimal(Console.ReadLine());
            
            BankAccount account = new BankAccount(ownerName, initialBalance);
            
            Console.WriteLine("\nAccount created successfully!");
            account.DisplayAccountInfo();
            
            // Menu-driven interface
            bool running = true;
            while (running)
            {
                Console.WriteLine("\n=== Menu ===");
                Console.WriteLine("1. Deposit");
                Console.WriteLine("2. Withdraw");
                Console.WriteLine("3. Check Balance");
                Console.WriteLine("4. Display Account Info");
                Console.WriteLine("5. Exit");
                Console.Write("Choose an option (1-5): ");
                
                string choice = Console.ReadLine();
                
                switch (choice)
                {
                    case "1":
                        Console.Write("Enter deposit amount: $");
                        decimal depositAmount = Convert.ToDecimal(Console.ReadLine());
                        account.Deposit(depositAmount);
                        Console.WriteLine($"Deposited ${depositAmount:F2}");
                        break;
                        
                    case "2":
                        Console.Write("Enter withdrawal amount: $");
                        decimal withdrawalAmount = Convert.ToDecimal(Console.ReadLine());
                        if (account.Withdraw(withdrawalAmount))
                        {
                            Console.WriteLine($"Withdrew ${withdrawalAmount:F2}");
                        }
                        else
                        {
                            Console.WriteLine("Insufficient funds!");
                        }
                        break;
                        
                    case "3":
                        Console.WriteLine($"Current balance: ${account.GetBalance():F2}");
                        break;
                        
                    case "4":
                        account.DisplayAccountInfo();
                        break;
                        
                    case "5":
                        Console.WriteLine("Goodbye!");
                        running = false;
                        break;
                        
                    default:
                        Console.WriteLine("Invalid option!");
                        break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error: {ex.Message}");
        }
    }
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Not validating input in constructor
public BankAccount(string ownerName, decimal initialBalance)
{
    OwnerName = ownerName; // Could be null or empty
    Balance = initialBalance; // Could be negative
}

// Not checking for negative amounts
public void Deposit(decimal amount)
{
    Balance += amount; // Could add negative amount
}

// Not checking for insufficient funds
public void Withdraw(decimal amount)
{
    Balance -= amount; // Could go negative
}

// Using public setter for balance
public decimal Balance { get; set; } // Anyone can modify balance

// Not handling exceptions
decimal amount = Convert.ToDecimal(Console.ReadLine()); // Will crash on invalid input
```

### ✅ Do This Instead:
```csharp
// Validate input in constructor
public BankAccount(string ownerName, decimal initialBalance)
{
    if (string.IsNullOrWhiteSpace(ownerName))
        throw new ArgumentException("Owner name cannot be empty");
        
    if (initialBalance < 0)
        throw new ArgumentException("Initial balance cannot be negative");
        
    OwnerName = ownerName;
    Balance = initialBalance;
}

// Validate deposit amount
public void Deposit(decimal amount)
{
    if (amount <= 0)
        throw new ArgumentException("Deposit amount must be positive");
        
    Balance += amount;
}

// Check for sufficient funds
public bool Withdraw(decimal amount)
{
    if (amount <= 0)
        throw new ArgumentException("Withdrawal amount must be positive");
        
    if (amount > Balance)
        return false; // Insufficient funds
        
    Balance -= amount;
    return true;
}

// Use private setter for balance
public decimal Balance { get; private set; }

// Handle exceptions
try
{
    decimal amount = Convert.ToDecimal(Console.ReadLine());
}
catch (FormatException)
{
    Console.WriteLine("Please enter a valid number!");
}
```

## Testing Your Program

### Test Cases to Try:
1. **Create account**: Enter valid name and initial balance
2. **Deposit**: Add money to account
3. **Withdraw within balance**: Withdraw amount less than balance
4. **Withdraw over balance**: Try to withdraw more than available → Should fail
5. **Invalid input**: Enter "abc" for amounts → Should handle gracefully
6. **Negative amounts**: Try negative deposits/withdrawals → Should show error
7. **Empty name**: Try to create account with empty name → Should show error

### How to Run:
```bash
dotnet run
```

## Submission Checklist
- [ ] Program compiles without errors
- [ ] BankAccount class has all required properties
- [ ] Constructor validates input properly
- [ ] Deposit method works correctly
- [ ] Withdraw method checks for sufficient funds
- [ ] GetBalance method returns current balance
- [ ] DisplayAccountInfo shows all account details
- [ ] Handles invalid input gracefully
- [ ] Uses proper encapsulation (private setters)
- [ ] Code is properly formatted and readable

## Bonus Challenges
1. **Transaction History**: Keep track of all deposits and withdrawals
2. **Overdraft Protection**: Allow negative balance up to a limit
3. **Interest Calculation**: Add monthly interest to account
4. **Multiple Accounts**: Support checking and savings accounts
5. **Account Transfer**: Transfer money between accounts
6. **File Persistence**: Save account data to a file
7. **Account Types**: Different interest rates for different account types
8. **Monthly Statements**: Generate monthly account statements

## Resources
- [C# Classes](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/)
- [Properties](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/properties)
- [Access Modifiers](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)

## Next Steps
Once you complete this project, you'll be ready for Project 6: Weather Data Analyzer, where you'll learn about file I/O, data analysis, and LINQ operations. 