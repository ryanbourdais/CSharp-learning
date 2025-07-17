# Project 3: Number Guessing Game

## Assignment Summary
Create an interactive game where the computer picks a random number and the player tries to guess it. The game provides hints about whether the guess is too high or too low, and tracks the number of attempts.

## Learning Objectives
- Generate random numbers using the Random class
- Use loops (while, for) for game logic
- Implement conditional statements (if/else)
- Track game state and variables
- Handle user input validation
- Create engaging user experience

## Project Requirements

### Basic Requirements
1. **Generate Random Number**: Computer picks a number between 1 and 100
2. **Get Player Guess**: Ask player to enter their guess
3. **Provide Feedback**: Tell player if guess is too high, too low, or correct
4. **Track Attempts**: Count and display number of guesses
5. **Game Loop**: Continue until correct guess or max attempts reached
6. **Play Again**: Ask if player wants to play another round

### Advanced Requirements (Optional)
- Set different difficulty levels (different number ranges)
- Add a scoring system based on number of attempts
- Keep track of best score (lowest attempts)
- Add a timer to track how long the game takes
- Allow player to set the range of numbers

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new console -n NumberGuessingGame
cd NumberGuessingGame
```

### Step 2: Plan Your Game Logic
Think about the flow:
1. Generate random number
2. Welcome player and explain rules
3. Get player's guess
4. Compare guess to target number
5. Provide feedback
6. Repeat until correct or max attempts
7. Ask to play again

### Step 3: Start with Basic Structure
```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        bool playAgain = true;
        
        while (playAgain)
        {
            // Generate random number
            // Play one game
            // Ask to play again
        }
    }
}
```

## Hints and Code Examples

### Generating Random Numbers
```csharp
Random random = new Random();
int targetNumber = random.Next(1, 101); // Generates number between 1 and 100
```

### Basic Game Loop
```csharp
int attempts = 0;
int maxAttempts = 10;
bool gameWon = false;

while (attempts < maxAttempts && !gameWon)
{
    Console.Write($"Enter your guess (1-100): ");
    int guess = Convert.ToInt32(Console.ReadLine());
    attempts++;
    
    if (guess == targetNumber)
    {
        Console.WriteLine($"Congratulations! You guessed it in {attempts} attempts!");
        gameWon = true;
    }
    else if (guess < targetNumber)
    {
        Console.WriteLine("Too low! Try again.");
    }
    else
    {
        Console.WriteLine("Too high! Try again.");
    }
    
    if (!gameWon && attempts < maxAttempts)
    {
        Console.WriteLine($"Attempts remaining: {maxAttempts - attempts}");
    }
}
```

### Complete Game Implementation
```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        Random random = new Random();
        bool playAgain = true;
        
        Console.WriteLine("=== Number Guessing Game ===");
        Console.WriteLine("I'm thinking of a number between 1 and 100.");
        Console.WriteLine("Can you guess it?\n");
        
        while (playAgain)
        {
            int targetNumber = random.Next(1, 101);
            int attempts = 0;
            int maxAttempts = 10;
            bool gameWon = false;
            
            Console.WriteLine($"\nNew game! You have {maxAttempts} attempts.");
            
            while (attempts < maxAttempts && !gameWon)
            {
                Console.Write($"Enter your guess (1-100): ");
                
                try
                {
                    int guess = Convert.ToInt32(Console.ReadLine());
                    attempts++;
                    
                    if (guess < 1 || guess > 100)
                    {
                        Console.WriteLine("Please enter a number between 1 and 100!");
                        attempts--; // Don't count invalid attempts
                        continue;
                    }
                    
                    if (guess == targetNumber)
                    {
                        Console.WriteLine($"\n🎉 Congratulations! You guessed it in {attempts} attempts!");
                        gameWon = true;
                    }
                    else if (guess < targetNumber)
                    {
                        Console.WriteLine("📈 Too low! Try again.");
                    }
                    else
                    {
                        Console.WriteLine("📉 Too high! Try again.");
                    }
                    
                    if (!gameWon && attempts < maxAttempts)
                    {
                        Console.WriteLine($"Attempts remaining: {maxAttempts - attempts}");
                    }
                }
                catch (FormatException)
                {
                    Console.WriteLine("Please enter a valid number!");
                    attempts--; // Don't count invalid attempts
                }
            }
            
            if (!gameWon)
            {
                Console.WriteLine($"\n😔 Game over! The number was {targetNumber}.");
            }
            
            Console.Write("\nPlay again? (y/n): ");
            string response = Console.ReadLine().ToLower();
            playAgain = (response == "y" || response == "yes");
        }
        
        Console.WriteLine("Thanks for playing! 👋");
    }
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Creating Random object inside the loop
while (playAgain)
{
    Random random = new Random(); // Wrong! Creates new instance each time
    int targetNumber = random.Next(1, 101);
    // ...
}

// Not handling invalid input
int guess = Convert.ToInt32(Console.ReadLine()); // Will crash on "abc"

// Infinite loop without exit condition
while (true)
{
    // No way to exit!
}

// Not tracking attempts properly
int attempts = 0;
while (attempts < maxAttempts)
{
    int guess = Convert.ToInt32(Console.ReadLine());
    // Forgot to increment attempts!
}

// Using wrong range for random
int targetNumber = random.Next(0, 100); // Includes 0, excludes 100
```

### ✅ Do This Instead:
```csharp
// Create Random object once, outside the loop
Random random = new Random();
while (playAgain)
{
    int targetNumber = random.Next(1, 101); // 1 to 100 inclusive
    // ...
}

// Handle invalid input with try-catch
try
{
    int guess = Convert.ToInt32(Console.ReadLine());
    attempts++;
}
catch (FormatException)
{
    Console.WriteLine("Please enter a valid number!");
    attempts--; // Don't count invalid attempts
}

// Always have an exit condition
while (attempts < maxAttempts && !gameWon)
{
    // Game logic
}

// Track attempts properly
int attempts = 0;
while (attempts < maxAttempts)
{
    int guess = Convert.ToInt32(Console.ReadLine());
    attempts++; // Always increment
    // ...
}

// Use correct range for random
int targetNumber = random.Next(1, 101); // 1 to 100 inclusive
```

## Testing Your Program

### Test Cases to Try:
1. **Normal gameplay**: Guess correctly within 10 attempts
2. **Invalid input**: Enter "abc" → Should handle gracefully
3. **Out of range**: Enter 0 or 101 → Should show error
4. **Max attempts**: Take 10 wrong guesses → Should show game over
5. **Play again**: Choose "y" → Should start new game
6. **Exit**: Choose "n" → Should exit cleanly

### How to Run:
```bash
dotnet run
```

## Submission Checklist
- [ ] Program compiles without errors
- [ ] Generates random number between 1-100
- [ ] Accepts player guesses
- [ ] Provides correct feedback (too high/low/correct)
- [ ] Tracks and displays number of attempts
- [ ] Handles invalid input gracefully
- [ ] Limits maximum attempts
- [ ] Asks to play again
- [ ] Exits cleanly when requested
- [ ] Code is properly formatted and readable

## Bonus Challenges
1. **Difficulty Levels**: Easy (1-50), Medium (1-100), Hard (1-200)
2. **Scoring System**: 100 points minus 10 per attempt
3. **Best Score Tracking**: Save and display best score
4. **Timer**: Track how long each game takes
5. **Custom Range**: Let player set the number range
6. **Hints**: Provide additional hints like "very close" or "way off"
7. **Statistics**: Track average attempts across multiple games

## Resources
- [Random Class](https://docs.microsoft.com/en-us/dotnet/api/system.random)
- [While Loop](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/statements/iteration-statements#the-while-statement)
- [Exception Handling](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/try-catch)

## Next Steps
Once you complete this project, you'll be ready for Project 4: To-Do List Manager, where you'll learn about collections, string manipulation, and data management. 