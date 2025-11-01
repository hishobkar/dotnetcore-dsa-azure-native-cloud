
# Task 3: Count Character Frequency - .NET Core Implementation Guide

## Problem Statement
Write logic in .NET Core to count the frequency of each character in a string and display results in a readable format.

**Example:**  
Input: `"hello world"`  
Output:  
```
h: 1  
e: 1  
l: 3  
o: 2  
' ': 1  
w: 1  
r: 1  
d: 1
```

---

## Objective
Implement logic that calculates how often each character appears in a string, presenting the output clearly with best practices for .NET Core development.

---

## Estimated Time **45 minutes**

---

## Tech Stack
- **.NET Core** (C#)
- **Console Application** or **Minimal API** (your choice)
- **xUnit** (optional, for unit testing)

---

## Prerequisites
- .NET Core SDK 9.0 (or .NET 6/7/8)
- Visual Studio 2022 / VS Code / Rider
- Understanding of C# dictionaries and string handling

---

## Architecture

### Project Structure
```
CharacterFrequency/
├── CharacterFrequency.csproj
├── Program.cs
├── Services/
│   └── CharacterFrequencyService.cs
├── Extensions/
│   └── StringExtensions.cs
└── Tests/ (optional)
    └── CharacterFrequencyServiceTests.cs
```

---

## Implementation Approaches

### Approach 1: Dictionary-Based Counting

```
public static Dictionary<char, int> CountCharacterFrequency(string input)
{
    var frequency = new Dictionary<char, int>();
    if (string.IsNullOrEmpty(input))
        return frequency;

    foreach (var ch in input)
    {
        if (frequency.ContainsKey(ch))
            frequency[ch]++;
        else
            frequency[ch] = 1;
    }
    return frequency;
}
```

### Approach 2: LINQ GroupBy (Concise)

```
public static Dictionary<char, int> CountCharacterFrequencyLinq(string input)
{
    if (string.IsNullOrEmpty(input))
        return new Dictionary<char, int>();

    return input.GroupBy(c => c)
                .ToDictionary(g => g.Key, g => g.Count());
}
```

---

## Step-by-Step Implementation

### Step 1: Create New .NET Core Console Project
```
dotnet new console -n CharacterFrequency
cd CharacterFrequency
```

### Step 2: Create CharacterFrequencyService Class

**File:** `Services/CharacterFrequencyService.cs`

```
using System;
using System.Collections.Generic;
using System.Linq;

namespace CharacterFrequency.Services;

public class CharacterFrequencyService
{
    public Dictionary<char, int> CountCharacterFrequency(string input)
    {
        var frequency = new Dictionary<char, int>();
        if (string.IsNullOrEmpty(input))
            return frequency;

        foreach (var ch in input)
        {
            if (frequency.ContainsKey(ch))
                frequency[ch]++;
            else
                frequency[ch] = 1;
        }
        return frequency;
    }

    public Dictionary<char, int> CountCharacterFrequencyLinq(string input)
    {
        if (string.IsNullOrEmpty(input))
            return new Dictionary<char, int>();

        return input.GroupBy(c => c)
                    .ToDictionary(g => g.Key, g => g.Count());
    }

    public void PrintFrequency(Dictionary<char, int> freq)
    {
        foreach (var kvp in freq.OrderBy(k => k.Key))
        {
            var displayChar = kvp.Key == ' ' ? "' '" : kvp.Key.ToString();
            Console.WriteLine($"{displayChar}: {kvp.Value}");
        }
    }
}
```

### Step 3: Create Extension Method (Optional)

**File:** `Extensions/StringExtensions.cs`

```
using System.Collections.Generic;
using System.Linq;

namespace CharacterFrequency.Extensions;

public static class StringExtensions
{
    public static Dictionary<char, int> CharacterFrequency(this string input)
    {
        if (string.IsNullOrEmpty(input))
            return new Dictionary<char, int>();

        return input.GroupBy(c => c)
                    .ToDictionary(g => g.Key, g => g.Count());
    }
}
```

### Step 4: Update Program.cs

**File:** `Program.cs`

```
using CharacterFrequency.Services;
using CharacterFrequency.Extensions;

namespace CharacterFrequency;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("=== Character Frequency Counter ===\n");

        var service = new CharacterFrequencyService();

        string[] testCases = 
        {
            "hello world",
            "C# Programming",
            "Mississippi",
            "123123!",
            "",
            "   ",
            "AaBbCc"
        };

        Console.WriteLine("Testing character frequency:\n");
        foreach (var testCase in testCases)
        {
            Console.WriteLine($"Input: \"{testCase}\"");
            Console.WriteLine("Dictionary Counting:");
            var freq = service.CountCharacterFrequency(testCase);
            service.PrintFrequency(freq);

            Console.WriteLine("LINQ GroupBy Counting:");
            var linqFreq = service.CountCharacterFrequencyLinq(testCase);
            service.PrintFrequency(linqFreq);

            Console.WriteLine("Extension Method Counting:");
            var extFreq = testCase.CharacterFrequency();
            service.PrintFrequency(extFreq);

            Console.WriteLine(new string('-', 40));
        }

        // Interactive mode
        Console.WriteLine("\n=== Interactive Mode ===");
        Console.WriteLine("Enter a string to analyze character frequency (or 'exit' to quit):");

        while (true)
        {
            Console.Write("\nInput: ");
            string? input = Console.ReadLine();

            if (string.IsNullOrEmpty(input) || input.ToLower() == "exit")
                break;

            var freq = service.CountCharacterFrequency(input);
            Console.WriteLine("Frequency:");
            service.PrintFrequency(freq);
        }

        Console.WriteLine("\nThank you for using Character Frequency Counter!");
    }
}
```

### Step 5: Run the Application
```
dotnet run
```

---

## Unit Tests (Optional - xUnit)

### Create Test Project
```
dotnet new xunit -n CharacterFrequency.Tests
dotnet add CharacterFrequency.Tests reference CharacterFrequency
```

**File:** `CharacterFrequencyServiceTests.cs`

```
using CharacterFrequency.Services;
using Xunit;
using System.Collections.Generic;

namespace CharacterFrequency.Tests;

public class CharacterFrequencyServiceTests
{
    private readonly CharacterFrequencyService _service = new();

    [Fact]
    public void CountCharacterFrequency_ShouldReturnCorrectCounts()
    {
        var input = "hello";
        var expected = new Dictionary<char, int>
        {
            {'h', 1},
            {'e', 1},
            {'l', 2},
            {'o', 1}
        };

        var result = _service.CountCharacterFrequency(input);

        Assert.Equal(expected, result);
    }

    [Fact]
    public void CountCharacterFrequency_EmptyInput_ReturnsEmptyDict()
    {
        var result = _service.CountCharacterFrequency("");
        Assert.Empty(result);
    }

    [Fact]
    public void CountCharacterFrequency_SpecialCharsHandled()
    {
        var input = "123!!";
        var expected = new Dictionary<char, int>
        {
            {'1', 1},
            {'2', 1},
            {'3', 1},
            {'!', 2}
        };

        var result = _service.CountCharacterFrequency(input);

        Assert.Equal(expected, result);
    }
}
```

### Run Tests
```
dotnet test
```

---

## Edge Cases to Handle

- **Null/empty input:** Return empty dictionary
- **Whitespace characters:** Counted separately (e.g. space `' '`)
- **Special characters:** Counted as-is (`!`, `@`, etc.)
- **Case sensitivity:** 'a' and 'A' counted separately unless normalized
- **Unicode characters:** Should be counted correctly by .NET string methods

---

## Key Learning Points

- Use of dictionaries for frequency counting  
- LINQ techniques for data grouping  
- Handling edge cases (empty, special chars, whitespace)  
- Extension methods for clean usage  
- Formatting output for readability  
- Writing and running automated tests

---

## Workflow

```
graph TD
    A[Start] --> B[Get Input String]
    B --> C{Null/Empty?}
    C -->|Yes| D[Return Empty Dictionary]
    C -->|No| E[Iterate Each Character]
    E --> F[Update Frequency Dictionary]
    F --> G[Sort and Print]
    G --> H[End]
```

---

## Expected Output

```
=== Character Frequency Counter ===

Input: "hello world"
Dictionary Counting:
' ': 1
d: 1
e: 1
h: 1
l: 3
o: 2
r: 1
w: 1
LINQ GroupBy Counting:
' ': 1
d: 1
e: 1
h: 1
l: 3
o: 2
r: 1
w: 1
Extension Method Counting:
' ': 1
d: 1
e: 1
h: 1
l: 3
o: 2
r: 1
w: 1
----------------------------------------
```

---

## Extensions & Challenges

### Beginner Level
- Ignore spaces or count only letters
- Display results sorted by descending frequency

### Intermediate Level
- Normalize case before counting
- Display as a bar chart or graphical output

### Advanced Level
- Create a web API endpoint for frequency analysis
- Export results to JSON or CSV
- Handle and group Unicode categories (e.g., all digit characters)

---

## Summary

This task builds critical string analysis skills for counting character frequencies using dictionary and LINQ methods. You'll learn best practices in output formatting, modular code, and robust testing in .NET Core.

**Completion Checklist:**
- [ ] Implement at least 2 counting methods
- [ ] Handle edge cases (empty, whitespace, special chars)
- [ ] Output formatted frequency table
- [ ] Write and run unit tests
- [ ] Document your code
- [ ] Test with variety of examples

---