# Project 6: Weather Data Analyzer

## Assignment Summary
Create a program that reads weather data from a CSV file and provides analysis including average temperature, highest/lowest temperatures, and weather patterns. This project introduces file I/O, data analysis, and LINQ operations.

## Learning Objectives
- Read and parse CSV files
- Work with collections and data analysis
- Use LINQ for data querying
- Implement basic algorithms (sorting, searching)
- Handle file operations and exceptions
- Practice data visualization concepts

## Project Requirements

### Basic Requirements
1. **Read CSV File**: Load weather data from a CSV file
2. **Parse Data**: Convert CSV rows to structured data
3. **Calculate Statistics**: Find average, min, max temperatures
4. **Display Results**: Show analysis in formatted output
5. **Handle Errors**: Manage missing or invalid data gracefully

### Advanced Requirements (Optional)
- Group data by month or season
- Calculate temperature trends
- Find weather patterns (rainy days, etc.)
- Export analysis results to file
- Create simple charts/visualizations
- Add data validation

## Step-by-Step Instructions

### Step 1: Create Your Project
```bash
dotnet new console -n WeatherDataAnalyzer
cd WeatherDataAnalyzer
```

### Step 2: Create Sample Data
Create a file called `weather_data.csv` with sample data:

```csv
Date,Temperature,Humidity,Weather
2024-01-01,15.5,65,Sunny
2024-01-02,12.3,70,Cloudy
2024-01-03,18.7,55,Sunny
2024-01-04,8.2,85,Rainy
2024-01-05,22.1,45,Sunny
2024-01-06,14.8,75,Cloudy
2024-01-07,25.3,40,Sunny
2024-01-08,6.5,90,Rainy
2024-01-09,19.2,60,Sunny
2024-01-10,11.7,80,Cloudy
```

### Step 3: Design Your Data Structure
Create a class to represent weather data:

```csharp
public class WeatherRecord
{
    public DateTime Date { get; set; }
    public double Temperature { get; set; }
    public int Humidity { get; set; }
    public string Weather { get; set; }
}
```

## Hints and Code Examples

### Reading CSV File
```csharp
public static List<WeatherRecord> LoadWeatherData(string filePath)
{
    List<WeatherRecord> weatherData = new List<WeatherRecord>();
    
    try
    {
        string[] lines = File.ReadAllLines(filePath);
        
        // Skip header line
        for (int i = 1; i < lines.Length; i++)
        {
            string[] parts = lines[i].Split(',');
            if (parts.Length >= 4)
            {
                WeatherRecord record = new WeatherRecord
                {
                    Date = DateTime.Parse(parts[0]),
                    Temperature = double.Parse(parts[1]),
                    Humidity = int.Parse(parts[2]),
                    Weather = parts[3]
                };
                weatherData.Add(record);
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error reading file: {ex.Message}");
    }
    
    return weatherData;
}
```

### Basic Analysis Methods
```csharp
public static void AnalyzeWeatherData(List<WeatherRecord> data)
{
    if (data.Count == 0)
    {
        Console.WriteLine("No weather data available.");
        return;
    }
    
    // Basic statistics
    double avgTemperature = data.Average(r => r.Temperature);
    double maxTemperature = data.Max(r => r.Temperature);
    double minTemperature = data.Min(r => r.Temperature);
    
    // Weather patterns
    var sunnyDays = data.Where(r => r.Weather.ToLower() == "sunny").Count();
    var rainyDays = data.Where(r => r.Weather.ToLower() == "rainy").Count();
    var cloudyDays = data.Where(r => r.Weather.ToLower() == "cloudy").Count();
    
    // Display results
    Console.WriteLine("=== Weather Data Analysis ===");
    Console.WriteLine($"Total days analyzed: {data.Count}");
    Console.WriteLine($"Average temperature: {avgTemperature:F1}°C");
    Console.WriteLine($"Highest temperature: {maxTemperature:F1}°C");
    Console.WriteLine($"Lowest temperature: {minTemperature:F1}°C");
    Console.WriteLine($"Temperature range: {maxTemperature - minTemperature:F1}°C");
    Console.WriteLine();
    Console.WriteLine("Weather Patterns:");
    Console.WriteLine($"Sunny days: {sunnyDays}");
    Console.WriteLine($"Rainy days: {rainyDays}");
    Console.WriteLine($"Cloudy days: {cloudyDays}");
}
```

### Complete Implementation
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

public class WeatherRecord
{
    public DateTime Date { get; set; }
    public double Temperature { get; set; }
    public int Humidity { get; set; }
    public string Weather { get; set; }
}

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("=== Weather Data Analyzer ===");
        
        string filePath = "weather_data.csv";
        
        if (!File.Exists(filePath))
        {
            Console.WriteLine($"Error: {filePath} not found!");
            Console.WriteLine("Please create a weather_data.csv file with your data.");
            return;
        }
        
        List<WeatherRecord> weatherData = LoadWeatherData(filePath);
        
        if (weatherData.Count > 0)
        {
            AnalyzeWeatherData(weatherData);
            ShowAdvancedAnalysis(weatherData);
        }
        else
        {
            Console.WriteLine("No valid weather data found.");
        }
    }
    
    static List<WeatherRecord> LoadWeatherData(string filePath)
    {
        List<WeatherRecord> weatherData = new List<WeatherRecord>();
        
        try
        {
            string[] lines = File.ReadAllLines(filePath);
            
            for (int i = 1; i < lines.Length; i++) // Skip header
            {
                try
                {
                    string[] parts = lines[i].Split(',');
                    if (parts.Length >= 4)
                    {
                        WeatherRecord record = new WeatherRecord
                        {
                            Date = DateTime.Parse(parts[0]),
                            Temperature = double.Parse(parts[1]),
                            Humidity = int.Parse(parts[2]),
                            Weather = parts[3]
                        };
                        weatherData.Add(record);
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine($"Warning: Skipping invalid line {i + 1}: {ex.Message}");
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error reading file: {ex.Message}");
        }
        
        return weatherData;
    }
    
    static void AnalyzeWeatherData(List<WeatherRecord> data)
    {
        Console.WriteLine("=== Basic Weather Analysis ===");
        Console.WriteLine($"Total days analyzed: {data.Count}");
        Console.WriteLine($"Average temperature: {data.Average(r => r.Temperature):F1}°C");
        Console.WriteLine($"Highest temperature: {data.Max(r => r.Temperature):F1}°C");
        Console.WriteLine($"Lowest temperature: {data.Min(r => r.Temperature):F1}°C");
        Console.WriteLine($"Temperature range: {data.Max(r => r.Temperature) - data.Min(r => r.Temperature):F1}°C");
        Console.WriteLine($"Average humidity: {data.Average(r => r.Humidity):F0}%");
    }
    
    static void ShowAdvancedAnalysis(List<WeatherRecord> data)
    {
        Console.WriteLine("\n=== Advanced Analysis ===");
        
        // Weather patterns
        var weatherGroups = data.GroupBy(r => r.Weather.ToLower())
                               .Select(g => new { Weather = g.Key, Count = g.Count() })
                               .OrderByDescending(x => x.Count);
        
        Console.WriteLine("Weather Patterns:");
        foreach (var group in weatherGroups)
        {
            Console.WriteLine($"{group.Weather}: {group.Count} days");
        }
        
        // Temperature analysis
        var hotDays = data.Where(r => r.Temperature > 20).Count();
        var coldDays = data.Where(r => r.Temperature < 10).Count();
        var mildDays = data.Count - hotDays - coldDays;
        
        Console.WriteLine($"\nTemperature Categories:");
        Console.WriteLine($"Hot days (>20°C): {hotDays}");
        Console.WriteLine($"Mild days (10-20°C): {mildDays}");
        Console.WriteLine($"Cold days (<10°C): {coldDays}");
        
        // Best and worst days
        var bestDay = data.OrderByDescending(r => r.Temperature).First();
        var worstDay = data.OrderBy(r => r.Temperature).First();
        
        Console.WriteLine($"\nBest day: {bestDay.Date:yyyy-MM-dd} ({bestDay.Temperature}°C, {bestDay.Weather})");
        Console.WriteLine($"Coldest day: {worstDay.Date:yyyy-MM-dd} ({worstDay.Temperature}°C, {worstDay.Weather})");
    }
}
```

## Common Mistakes to Avoid

### ❌ Don't Do This:
```csharp
// Not handling file not found
string[] lines = File.ReadAllLines(filePath); // Will crash if file doesn't exist

// Not parsing data safely
double temperature = double.Parse(parts[1]); // Will crash on invalid data

// Not checking array bounds
string weather = parts[3]; // Will crash if parts.Length < 4

// Not handling empty data
double avgTemp = data.Average(r => r.Temperature); // Will crash on empty list

// Not validating data
WeatherRecord record = new WeatherRecord
{
    Temperature = double.Parse(parts[1]) // Could be invalid
};
```

### ✅ Do This Instead:
```csharp
// Check if file exists
if (File.Exists(filePath))
{
    string[] lines = File.ReadAllLines(filePath);
}

// Parse safely with try-catch
try
{
    double temperature = double.Parse(parts[1]);
}
catch (FormatException)
{
    Console.WriteLine("Invalid temperature data");
}

// Check array bounds
if (parts.Length >= 4)
{
    string weather = parts[3];
}

// Check for empty data
if (data.Count > 0)
{
    double avgTemp = data.Average(r => r.Temperature);
}

// Validate data
if (double.TryParse(parts[1], out double temperature))
{
    WeatherRecord record = new WeatherRecord
    {
        Temperature = temperature
    };
}
```

## Testing Your Program

### Test Cases to Try:
1. **Valid data**: Use the sample CSV file → Should show analysis
2. **Missing file**: Delete weather_data.csv → Should show error message
3. **Invalid data**: Add invalid temperature values → Should skip bad lines
4. **Empty file**: Create empty CSV → Should handle gracefully
5. **Large dataset**: Add more weather data → Should work with any amount

### How to Run:
```bash
dotnet run
```

## Submission Checklist
- [ ] Program compiles without errors
- [ ] Reads CSV file successfully
- [ ] Parses weather data correctly
- [ ] Calculates basic statistics (avg, min, max)
- [ ] Shows weather patterns
- [ ] Handles missing or invalid data gracefully
- [ ] Displays results in formatted output
- [ ] Uses LINQ for data analysis
- [ ] Code is properly formatted and readable

## Bonus Challenges
1. **Monthly Analysis**: Group data by month and show trends
2. **Seasonal Patterns**: Analyze weather by seasons
3. **Data Export**: Save analysis results to a new file
4. **Temperature Trends**: Calculate if temperatures are rising/falling
5. **Weather Correlation**: Find relationships between temperature and humidity
6. **Data Visualization**: Create simple ASCII charts
7. **Multiple Files**: Analyze data from multiple CSV files
8. **Real-time Data**: Connect to a weather API

## Resources
- [File I/O](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/file-system/)
- [LINQ](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/)
- [Exception Handling](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/try-catch)

## Next Steps
Once you complete this project, you'll be ready for Project 7: Personal Website, where you'll learn about ASP.NET Core MVC and web development. 