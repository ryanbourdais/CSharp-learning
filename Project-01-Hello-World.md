# Project 1: Hello World and Basic Output

## Assignment Summary
Create your first C# program! You'll build a simple console application that greets the user and asks for their name, then provides a personalized greeting.

## Learning Objectives
- Install and set up your development environment
- Write your first C# program
- Understand basic program structure
- Work with console input and output
- Use string variables and concatenation

## Project Requirements

### Basic Requirements
1. **Program Setup**: Create a new C# console application
2. **Display Welcome Message**: Show "Hello, World!" when the program starts
3. **Ask for Name**: Prompt the user to enter their name
4. **Personalized Greeting**: Display a personalized message using their name
5. **Farewell**: End with a goodbye message

### Advanced Requirements (Optional)
- Ask for the user's age and include it in the greeting
- Add some basic formatting (like asterisks around the greeting)
- Handle empty input gracefully

## Step-by-Step Instructions

### Step 1: Environment Setup
1. Download and install Visual Studio Code
2. Install the .NET SDK from Microsoft's website
3. Open VS Code and install the C# extension
4. Open a terminal and navigate to your project folder

### Step 2: Create Your Project
```bash
dotnet new console -n HelloWorldApp
cd HelloWorldApp
```

### Step 3: Write Your Code
Replace the contents of `Program.cs` with your code. Start with this basic structure:

```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        // Your code goes here
    }
}
```

## Hints and Code Examples

### Basic Console Output
```csharp
Console.WriteLine("Hello, World!");
Console.Write("Enter your name: ");
```

### Getting User Input
```csharp
string name = Console.ReadLine();
```

### String Concatenation
```csharp
// Method 1: Using + operator
Console.WriteLine("Hello, " + name + "!");

// Method 2: Using string interpolation (recommended)
Console.WriteLine($"Hello, {name}!");
```

### Basic Program Structure
```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Hello, World!");
        
        Console.Write("What is your name? ");
        string name = Console.ReadLine();
        
        Console.WriteLine($"Nice to meet you, {name}!");
        Console.WriteLine("Goodbye!");
    }
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Missing semicolon
Console.WriteLine("Hello, World")

// Wrong casing (C# is case-sensitive)
console.WriteLine("Hello, World!");

// Forgetting to get user input before using it
string name;
Console.WriteLine($"Hello, {name}!"); // name is not initialized!

// Not handling empty input
string name = Console.ReadLine();
Console.WriteLine($"Hello, {name}!"); // Will show "Hello, !" if user just presses Enter
```

### ✅ Do This Instead:
```csharp
// Always end statements with semicolon
Console.WriteLine("Hello, World!");

// Use correct casing
Console.WriteLine("Hello, World!");

// Initialize variables before using them
Console.Write("Enter your name: ");
string name = Console.ReadLine();
Console.WriteLine($"Hello, {name}!");

// Handle empty input
Console.Write("Enter your name: ");
string name = Console.ReadLine();
if (string.IsNullOrEmpty(name))
{
    name = "Anonymous";
}
Console.WriteLine($"Hello, {name}!");
```

## Testing Your Program

### Test Cases to Try:
1. **Normal input**: Enter "John" → Should display "Hello, John!"
2. **Empty input**: Just press Enter → Should handle gracefully
3. **Special characters**: Try "Mary-Jane" or "José"
4. **Numbers**: Enter "123" → Should work fine (it's just text)

### How to Run:
```bash
dotnet run
```

## Submission Checklist
- [ ] Program compiles without errors
- [ ] Displays "Hello, World!" on startup
- [ ] Asks for user's name
- [ ] Displays personalized greeting
- [ ] Handles empty input gracefully
- [ ] Code is properly formatted and readable

## Bonus Challenges
1. **Add Age**: Ask for age and include it in the greeting
2. **Add Formatting**: Put asterisks around your greeting like `*** Hello, John! ***`
3. **Add Time**: Include the current time in your greeting
4. **Add Validation**: Make sure the name is not empty and has at least 2 characters

## Resources
- [C# Documentation](https://docs.microsoft.com/en-us/dotnet/csharp/)
- [Console Class Reference](https://docs.microsoft.com/en-us/dotnet/api/system.console)
- [String Interpolation](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/tokens/interpolated)

## Next Steps
Once you complete this project, you'll be ready for Project 2: Simple Calculator, where you'll learn about variables, data types, and arithmetic operations. 