# Project 2: Simple Calculator

## Assignment Summary
Build a console-based calculator that can perform basic arithmetic operations (addition, subtraction, multiplication, division) on two numbers entered by the user.

## Learning Objectives
- Work with different data types (int, double)
- Perform arithmetic operations
- Handle user input and validation
- Implement basic error handling
- Use conditional statements (if/else)

## Project Requirements

### Basic Requirements
1. **Display Menu**: Show a menu of available operations
2. **Get Numbers**: Ask user to enter two numbers
3. **Choose Operation**: Let user select which operation to perform
4. **Calculate Result**: Perform the selected operation
5. **Display Result**: Show the calculation and result
6. **Continue Option**: Ask if user wants to perform another calculation

### Advanced Requirements (Optional)
- Handle division by zero gracefully
- Support decimal numbers (use double instead of int)
- Add more operations (power, square root, percentage)
- Keep a history of calculations

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new console -n SimpleCalculator
cd SimpleCalculator
```

### Step 2: Plan Your Program Structure
Think about the flow:
1. Show menu
2. Get user choice
3. Get two numbers
4. Perform calculation
5. Show result
6. Ask to continue

### Step 3: Start with Basic Structure
```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        bool continueCalculating = true;
        
        while (continueCalculating)
        {
            // Show menu
            // Get user input
            // Perform calculation
            // Ask to continue
        }
    }
}
```

## Hints and Code Examples

### Displaying a Menu
```csharp
Console.WriteLine("=== Simple Calculator ===");
Console.WriteLine("1. Addition (+)");
Console.WriteLine("2. Subtraction (-)");
Console.WriteLine("3. Multiplication (*)");
Console.WriteLine("4. Division (/)");
Console.WriteLine("5. Exit");
Console.Write("Choose an operation (1-5): ");
```

### Getting User Input
```csharp
// Get operation choice
string choice = Console.ReadLine();

// Get numbers
Console.Write("Enter first number: ");
string input1 = Console.ReadLine();
double num1 = Convert.ToDouble(input1);

Console.Write("Enter second number: ");
string input2 = Console.ReadLine();
double num2 = Convert.ToDouble(input2);
```

### Performing Calculations
```csharp
double result = 0;

switch (choice)
{
    case "1":
        result = num1 + num2;
        Console.WriteLine($"{num1} + {num2} = {result}");
        break;
    case "2":
        result = num1 - num2;
        Console.WriteLine($"{num1} - {num2} = {result}");
        break;
    case "3":
        result = num1 * num2;
        Console.WriteLine($"{num1} * {num2} = {result}");
        break;
    case "4":
        if (num2 != 0)
        {
            result = num1 / num2;
            Console.WriteLine($"{num1} / {num2} = {result}");
        }
        else
        {
            Console.WriteLine("Error: Cannot divide by zero!");
        }
        break;
    default:
        Console.WriteLine("Invalid choice!");
        break;
}
```

### Complete Basic Calculator
```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        bool continueCalculating = true;
        
        while (continueCalculating)
        {
            Console.WriteLine("\n=== Simple Calculator ===");
            Console.WriteLine("1. Addition (+)");
            Console.WriteLine("2. Subtraction (-)");
            Console.WriteLine("3. Multiplication (*)");
            Console.WriteLine("4. Division (/)");
            Console.WriteLine("5. Exit");
            
            Console.Write("Choose an operation (1-5): ");
            string choice = Console.ReadLine();
            
            if (choice == "5")
            {
                Console.WriteLine("Goodbye!");
                continueCalculating = false;
                continue;
            }
            
            Console.Write("Enter first number: ");
            double num1 = Convert.ToDouble(Console.ReadLine());
            
            Console.Write("Enter second number: ");
            double num2 = Convert.ToDouble(Console.ReadLine());
            
            double result = 0;
            bool validOperation = true;
            
            switch (choice)
            {
                case "1":
                    result = num1 + num2;
                    Console.WriteLine($"{num1} + {num2} = {result}");
                    break;
                case "2":
                    result = num1 - num2;
                    Console.WriteLine($"{num1} - {num2} = {result}");
                    break;
                case "3":
                    result = num1 * num2;
                    Console.WriteLine($"{num1} * {num2} = {result}");
                    break;
                case "4":
                    if (num2 != 0)
                    {
                        result = num1 / num2;
                        Console.WriteLine($"{num1} / {num2} = {result}");
                    }
                    else
                    {
                        Console.WriteLine("Error: Cannot divide by zero!");
                        validOperation = false;
                    }
                    break;
                default:
                    Console.WriteLine("Invalid choice!");
                    validOperation = false;
                    break;
            }
            
            if (validOperation)
            {
                Console.Write("Calculate again? (y/n): ");
                string continueChoice = Console.ReadLine().ToLower();
                if (continueChoice != "y" && continueChoice != "yes")
                {
                    continueCalculating = false;
                }
            }
        }
    }
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Not handling invalid input
string input = Console.ReadLine();
int number = Convert.ToInt32(input); // Will crash if user enters "abc"

// Not checking for division by zero
double result = num1 / num2; // Will crash if num2 is 0

// Using wrong data type for calculations
int num1 = 5;
int num2 = 2;
int result = num1 / num2; // Result will be 2, not 2.5

// Not validating user choice
switch (choice)
{
    case "1": // Will only work if choice is exactly "1"
        // ...
}
```

### ✅ Do This Instead:
```csharp
// Handle invalid input with try-catch
try
{
    double number = Convert.ToDouble(Console.ReadLine());
}
catch (FormatException)
{
    Console.WriteLine("Please enter a valid number!");
    return;
}

// Check for division by zero
if (num2 != 0)
{
    double result = num1 / num2;
    Console.WriteLine($"Result: {result}");
}
else
{
    Console.WriteLine("Error: Cannot divide by zero!");
}

// Use double for decimal results
double num1 = 5;
double num2 = 2;
double result = num1 / num2; // Result will be 2.5

// Validate user choice
if (choice == "1" || choice == "2" || choice == "3" || choice == "4")
{
    // Perform calculation
}
else
{
    Console.WriteLine("Invalid choice!");
}
```

## Testing Your Program

### Test Cases to Try:
1. **Basic operations**: 10 + 5, 10 - 5, 10 * 5, 10 / 5
2. **Division by zero**: 10 / 0 → Should show error message
3. **Invalid input**: Enter "abc" for numbers → Should handle gracefully
4. **Decimal numbers**: 10.5 + 3.2
5. **Invalid choice**: Enter "6" for operation → Should show error
6. **Exit functionality**: Choose option 5 → Should exit cleanly

### How to Run:
```bash
dotnet run
```

## Submission Checklist
- [ ] Program compiles without errors
- [ ] Displays menu with all operations
- [ ] Handles all four basic operations correctly
- [ ] Prevents division by zero
- [ ] Handles invalid input gracefully
- [ ] Allows multiple calculations
- [ ] Exits cleanly when requested
- [ ] Code is properly formatted and readable

## Bonus Challenges
1. **Add More Operations**: Power (x^y), square root, percentage
2. **Add History**: Keep track of last 5 calculations
3. **Add Memory Functions**: Store and recall a number
4. **Add Scientific Calculator**: Trigonometric functions, logarithms
5. **Add Input Validation**: Ensure numbers are within reasonable range

## Resources
- [C# Data Types](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/built-in-types)
- [Switch Statement](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/statements/selection-statements#the-switch-statement)
- [Exception Handling](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/try-catch)

## Next Steps
Once you complete this project, you'll be ready for Project 3: Number Guessing Game, where you'll learn about random numbers, loops, and game logic. 