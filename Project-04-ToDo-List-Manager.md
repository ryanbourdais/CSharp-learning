# Project 4: To-Do List Manager

## Assignment Summary
Build a console application that allows users to manage their to-do items. Users can add new tasks, view all tasks, mark tasks as complete, and delete tasks. This project introduces collections, string manipulation, and basic data management.

## Learning Objectives
- Work with collections (List<T>)
- Implement CRUD operations (Create, Read, Update, Delete)
- Use string manipulation and formatting
- Design menu-driven interfaces
- Handle data persistence (in-memory)
- Practice object-oriented programming concepts

## Project Requirements

### Basic Requirements
1. **Display Menu**: Show options for managing to-do items
2. **Add Task**: Allow user to add new tasks with descriptions
3. **View Tasks**: Display all tasks with their status
4. **Mark Complete**: Allow user to mark tasks as done
5. **Delete Task**: Allow user to remove tasks
6. **Exit Option**: Clean way to exit the program

### Advanced Requirements (Optional)
- Add due dates to tasks
- Add priority levels (High, Medium, Low)
- Add categories/tags to tasks
- Save tasks to a file
- Search and filter tasks
- Add task descriptions

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new console -n TodoListManager
cd TodoListManager
```

### Step 2: Design Your Data Structure
Create a class to represent a to-do item:

```csharp
public class TodoItem
{
    public int Id { get; set; }
    public string Description { get; set; }
    public bool IsCompleted { get; set; }
    public DateTime CreatedDate { get; set; }
    
    public TodoItem(string description)
    {
        Description = description;
        IsCompleted = false;
        CreatedDate = DateTime.Now;
    }
}
```

### Step 3: Plan Your Program Structure
Think about the flow:
1. Show menu
2. Get user choice
3. Perform selected operation
4. Return to menu
5. Exit when requested

## Hints and Code Examples

### Creating a List to Store Tasks
```csharp
List<TodoItem> todoItems = new List<TodoItem>();
int nextId = 1;
```

### Displaying the Menu
```csharp
Console.WriteLine("\n=== To-Do List Manager ===");
Console.WriteLine("1. Add new task");
Console.WriteLine("2. View all tasks");
Console.WriteLine("3. Mark task as complete");
Console.WriteLine("4. Delete task");
Console.WriteLine("5. Exit");
Console.Write("Choose an option (1-5): ");
```

### Adding a New Task
```csharp
Console.Write("Enter task description: ");
string description = Console.ReadLine();

if (!string.IsNullOrWhiteSpace(description))
{
    TodoItem newTask = new TodoItem(description);
    newTask.Id = nextId++;
    todoItems.Add(newTask);
    Console.WriteLine("Task added successfully!");
}
else
{
    Console.WriteLine("Task description cannot be empty!");
}
```

### Viewing All Tasks
```csharp
if (todoItems.Count == 0)
{
    Console.WriteLine("No tasks found.");
}
else
{
    Console.WriteLine("\nYour To-Do List:");
    Console.WriteLine("ID | Status | Description");
    Console.WriteLine("---|--------|------------");
    
    foreach (var task in todoItems)
    {
        string status = task.IsCompleted ? "[✓]" : "[ ]";
        Console.WriteLine($"{task.Id,2} | {status,6} | {task.Description}");
    }
}
```

### Marking a Task as Complete
```csharp
Console.Write("Enter task ID to mark as complete: ");
if (int.TryParse(Console.ReadLine(), out int taskId))
{
    TodoItem task = todoItems.FirstOrDefault(t => t.Id == taskId);
    if (task != null)
    {
        task.IsCompleted = true;
        Console.WriteLine("Task marked as complete!");
    }
    else
    {
        Console.WriteLine("Task not found!");
    }
}
else
{
    Console.WriteLine("Invalid ID!");
}
```

### Deleting a Task
```csharp
Console.Write("Enter task ID to delete: ");
if (int.TryParse(Console.ReadLine(), out int taskId))
{
    TodoItem task = todoItems.FirstOrDefault(t => t.Id == taskId);
    if (task != null)
    {
        todoItems.Remove(task);
        Console.WriteLine("Task deleted successfully!");
    }
    else
    {
        Console.WriteLine("Task not found!");
    }
}
else
{
    Console.WriteLine("Invalid ID!");
}
```

### Complete Implementation
```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class TodoItem
{
    public int Id { get; set; }
    public string Description { get; set; }
    public bool IsCompleted { get; set; }
    public DateTime CreatedDate { get; set; }
    
    public TodoItem(string description)
    {
        Description = description;
        IsCompleted = false;
        CreatedDate = DateTime.Now;
    }
}

class Program
{
    static void Main(string[] args)
    {
        List<TodoItem> todoItems = new List<TodoItem>();
        int nextId = 1;
        bool running = true;
        
        Console.WriteLine("Welcome to To-Do List Manager!");
        
        while (running)
        {
            Console.WriteLine("\n=== To-Do List Manager ===");
            Console.WriteLine("1. Add new task");
            Console.WriteLine("2. View all tasks");
            Console.WriteLine("3. Mark task as complete");
            Console.WriteLine("4. Delete task");
            Console.WriteLine("5. Exit");
            Console.Write("Choose an option (1-5): ");
            
            string choice = Console.ReadLine();
            
            switch (choice)
            {
                case "1":
                    Console.Write("Enter task description: ");
                    string description = Console.ReadLine();
                    
                    if (!string.IsNullOrWhiteSpace(description))
                    {
                        TodoItem newTask = new TodoItem(description);
                        newTask.Id = nextId++;
                        todoItems.Add(newTask);
                        Console.WriteLine("Task added successfully!");
                    }
                    else
                    {
                        Console.WriteLine("Task description cannot be empty!");
                    }
                    break;
                    
                case "2":
                    if (todoItems.Count == 0)
                    {
                        Console.WriteLine("No tasks found.");
                    }
                    else
                    {
                        Console.WriteLine("\nYour To-Do List:");
                        Console.WriteLine("ID | Status | Description");
                        Console.WriteLine("---|--------|------------");
                        
                        foreach (var task in todoItems)
                        {
                            string status = task.IsCompleted ? "[✓]" : "[ ]";
                            Console.WriteLine($"{task.Id,2} | {status,6} | {task.Description}");
                        }
                    }
                    break;
                    
                case "3":
                    Console.Write("Enter task ID to mark as complete: ");
                    if (int.TryParse(Console.ReadLine(), out int completeId))
                    {
                        TodoItem taskToComplete = todoItems.FirstOrDefault(t => t.Id == completeId);
                        if (taskToComplete != null)
                        {
                            taskToComplete.IsCompleted = true;
                            Console.WriteLine("Task marked as complete!");
                        }
                        else
                        {
                            Console.WriteLine("Task not found!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Invalid ID!");
                    }
                    break;
                    
                case "4":
                    Console.Write("Enter task ID to delete: ");
                    if (int.TryParse(Console.ReadLine(), out int deleteId))
                    {
                        TodoItem taskToDelete = todoItems.FirstOrDefault(t => t.Id == deleteId);
                        if (taskToDelete != null)
                        {
                            todoItems.Remove(taskToDelete);
                            Console.WriteLine("Task deleted successfully!");
                        }
                        else
                        {
                            Console.WriteLine("Task not found!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Invalid ID!");
                    }
                    break;
                    
                case "5":
                    Console.WriteLine("Goodbye!");
                    running = false;
                    break;
                    
                default:
                    Console.WriteLine("Invalid option! Please choose 1-5.");
                    break;
            }
        }
    }
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Not checking for null or empty input
string description = Console.ReadLine();
TodoItem newTask = new TodoItem(description); // Could be empty

// Not handling invalid ID input
int taskId = Convert.ToInt32(Console.ReadLine()); // Will crash on "abc"

// Not checking if task exists before operations
todoItems.Remove(task); // task might be null

// Using wrong collection method
var task = todoItems.Find(t => t.Id == taskId); // Find doesn't exist in List<T>

// Not incrementing ID properly
TodoItem newTask = new TodoItem(description);
todoItems.Add(newTask); // Forgot to set ID
```

### ✅ Do This Instead:
```csharp
// Check for empty input
string description = Console.ReadLine();
if (!string.IsNullOrWhiteSpace(description))
{
    TodoItem newTask = new TodoItem(description);
    // Add task
}

// Handle invalid input with TryParse
if (int.TryParse(Console.ReadLine(), out int taskId))
{
    // Use taskId
}
else
{
    Console.WriteLine("Invalid ID!");
}

// Check if task exists
TodoItem task = todoItems.FirstOrDefault(t => t.Id == taskId);
if (task != null)
{
    // Perform operation
}
else
{
    Console.WriteLine("Task not found!");
}

// Use correct method for List<T>
var task = todoItems.FirstOrDefault(t => t.Id == taskId);

// Set ID properly
TodoItem newTask = new TodoItem(description);
newTask.Id = nextId++;
todoItems.Add(newTask);
```

## Testing Your Program

### Test Cases to Try:
1. **Add tasks**: Add 3-4 different tasks
2. **View tasks**: Should show all tasks with IDs and status
3. **Mark complete**: Mark a task as done, then view to see [✓]
4. **Delete task**: Delete a task, then view to confirm it's gone
5. **Invalid input**: Enter "abc" for ID → Should handle gracefully
6. **Empty input**: Try to add task with empty description → Should show error
7. **Non-existent ID**: Try to mark/delete task with wrong ID → Should show "not found"

### How to Run:
```bash
dotnet run
```

## Submission Checklist
- [ ] Program compiles without errors
- [ ] Displays menu with all options
- [ ] Can add new tasks with descriptions
- [ ] Can view all tasks with IDs and status
- [ ] Can mark tasks as complete
- [ ] Can delete tasks by ID
- [ ] Handles invalid input gracefully
- [ ] Exits cleanly when requested
- [ ] Code is properly formatted and readable
- [ ] Uses appropriate data structures (List<T>)

## Bonus Challenges
1. **Add Due Dates**: Include due dates for tasks
2. **Add Priorities**: High, Medium, Low priority levels
3. **Add Categories**: Organize tasks by category
4. **File Persistence**: Save tasks to a text file
5. **Search Function**: Search tasks by description
6. **Filter Options**: Show only completed or incomplete tasks
7. **Edit Tasks**: Allow editing task descriptions
8. **Statistics**: Show count of completed vs incomplete tasks

## Resources
- [List<T> Class](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.list-1)
- [LINQ Methods](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/)
- [String Methods](https://docs.microsoft.com/en-us/dotnet/api/system.string)

## Next Steps
Once you complete this project, you'll be ready for Project 5: Simple Bank Account, where you'll learn about classes, objects, and object-oriented programming concepts. 