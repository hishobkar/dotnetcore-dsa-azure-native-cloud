# Task 1: Reverse a String - .NET Core Implementation Guide

## Problem Statement
Write a program that takes a string input and returns the reversed string.

**Example:**
- Input: `"hello"`
- Output: `"olleh"`

---

## Objective
Implement a string reversal function in .NET Core demonstrating multiple approaches and best practices.

---

## Estimated Time **30 minutes**

---

## Tech Stack
- **.NET Core** (C#)
- **Console Application** or **Minimal API** (your choice)
- **xUnit** (optional, for unit testing)

---

## Prerequisites
- .NET Core SDK 9.0 (or .NET 6/7/8)
- Visual Studio 2022 / VS Code / Rider
- Basic understanding of C# strings and arrays

---

## Architecture

### Project Structure
```
StringReversal/
├── StringReversal.csproj
├── Program.cs
├── Services/
│   └── StringReverser.cs
├── Extensions/
│   └── StringExtensions.cs
└── Tests/ (optional)
    └── StringReverserTests.cs
```

---

## Implementation Approaches

### Approach 1: Using Built-in Methods (Array.Reverse)
**Time Complexity:** O(n)  
**Space Complexity:** O(n)

```
public static string ReverseUsingArrayReverse(string input)
{
    if (string.IsNullOrEmpty(input))
        return input;
    
    char[] charArray = input.ToCharArray();
    Array.Reverse(charArray);
    return new string(charArray);
}
```

### Approach 2: Using LINQ (Reverse)
**Time Complexity:** O(n)  
**Space Complexity:** O(n)

```
public static string ReverseUsingLinq(string input)
{
    if (string.IsNullOrEmpty(input))
        return input;
    
    return new string(input.Reverse().ToArray());
}
```

### Approach 3: Using For Loop (Manual)
**Time Complexity:** O(n)  
**Space Complexity:** O(n)

```
public static string ReverseUsingLoop(string input)
{
    if (string.IsNullOrEmpty(input))
        return input;
    
    char[] result = new char[input.Length];
    for (int i = 0; i < input.Length; i++)
    {
        result[i] = input[input.Length - 1 - i];
    }
    return new string(result);
}
```

### Approach 4: Using StringBuilder (Performance Optimized)
**Time Complexity:** O(n)  
**Space Complexity:** O(n)

```
public static string ReverseUsingStringBuilder(string input)
{
    if (string.IsNullOrEmpty(input))
        return input;
    
    StringBuilder sb = new StringBuilder(input.Length);
    for (int i = input.Length - 1; i >= 0; i--)
    {
        sb.Append(input[i]);
    }
    return sb.ToString();
}
```

### Approach 5: Using Span<T> (Memory Efficient - .NET Core)
**Time Complexity:** O(n)  
**Space Complexity:** O(1) - in-place

```
public static string ReverseUsingSpan(string input)
{
    if (string.IsNullOrEmpty(input))
        return input;
    
    Span<char> chars = input.ToCharArray();
    chars.Reverse();
    return new string(chars);
}
```

---

## Step-by-Step Implementation

### Step 1: Create New .NET Core Console Project
```
dotnet new console -n StringReversal
cd StringReversal
```

### Step 2: Create StringReverser Service Class

**File:** `Services/StringReverser.cs`

```
using System.Text;

namespace StringReversal.Services;

public class StringReverser
{
    /// <summary>
    /// Reverses a string using Array.Reverse method
    /// </summary>
    public string ReverseUsingArrayReverse(string input)
    {
        if (string.IsNullOrEmpty(input))
            return input;
        
        char[] charArray = input.ToCharArray();
        Array.Reverse(charArray);
        return new string(charArray);
    }

    /// <summary>
    /// Reverses a string using LINQ Reverse
    /// </summary>
    public string ReverseUsingLinq(string input)
    {
        if (string.IsNullOrEmpty(input))
            return input;
        
        return new string(input.Reverse().ToArray());
    }

    /// <summary>
    /// Reverses a string using manual loop
    /// </summary>
    public string ReverseUsingLoop(string input)
    {
        if (string.IsNullOrEmpty(input))
            return input;
        
        char[] result = new char[input.Length];
        for (int i = 0; i < input.Length; i++)
        {
            result[i] = input[input.Length - 1 - i];
        }
        return new string(result);
    }

    /// <summary>
    /// Reverses a string using StringBuilder (performance optimized)
    /// </summary>
    public string ReverseUsingStringBuilder(string input)
    {
        if (string.IsNullOrEmpty(input))
            return input;
        
        StringBuilder sb = new StringBuilder(input.Length);
        for (int i = input.Length - 1; i >= 0; i--)
        {
            sb.Append(input[i]);
        }
        return sb.ToString();
    }

    /// <summary>
    /// Reverses a string using Span<T> (memory efficient)
    /// </summary>
    public string ReverseUsingSpan(string input)
    {
        if (string.IsNullOrEmpty(input))
            return input;
        
        Span<char> chars = input.ToCharArray();
        chars.Reverse();
        return new string(chars);
    }
}
```

### Step 3: Create Extension Method (Optional)

**File:** `Extensions/StringExtensions.cs`

```
namespace StringReversal.Extensions;

public static class StringExtensions
{
    public static string Reverse(this string input)
    {
        if (string.IsNullOrEmpty(input))
            return input;
        
        char[] charArray = input.ToCharArray();
        Array.Reverse(charArray);
        return new string(charArray);
    }
}
```

### Step 4: Update Program.cs

**File:** `Program.cs`

```
using StringReversal.Services;
using StringReversal.Extensions;

namespace StringReversal;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("=== String Reversal Program ===\n");

        var reverser = new StringReverser();

        // Example test cases
        string[] testCases = 
        {
            "hello",
            "world",
            "C# Programming",
            "12345",
            "A",
            "",
            "racecar",
            "Hello, World!"
        };

        Console.WriteLine("Testing different reversal methods:\n");

        foreach (var testCase in testCases)
        {
            Console.WriteLine($"Original: '{testCase}'");
            Console.WriteLine($"Array.Reverse: '{reverser.ReverseUsingArrayReverse(testCase)}'");
            Console.WriteLine($"LINQ: '{reverser.ReverseUsingLinq(testCase)}'");
            Console.WriteLine($"Loop: '{reverser.ReverseUsingLoop(testCase)}'");
            Console.WriteLine($"StringBuilder: '{reverser.ReverseUsingStringBuilder(testCase)}'");
            Console.WriteLine($"Span<T>: '{reverser.ReverseUsingSpan(testCase)}'");
            Console.WriteLine($"Extension: '{testCase.Reverse()}'");
            Console.WriteLine(new string('-', 50));
            Console.WriteLine();
        }

        // Interactive mode
        Console.WriteLine("\n=== Interactive Mode ===");
        Console.WriteLine("Enter a string to reverse (or 'exit' to quit):");

        while (true)
        {
            Console.Write("\nInput: ");
            string? input = Console.ReadLine();

            if (string.IsNullOrEmpty(input) || input.ToLower() == "exit")
                break;

            string reversed = reverser.ReverseUsingArrayReverse(input);
            Console.WriteLine($"Reversed: {reversed}");
        }

        Console.WriteLine("\nThank you for using String Reversal Program!");
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
dotnet new xunit -n StringReversal.Tests
dotnet add StringReversal.Tests reference StringReversal
```

**File:** `StringReverserTests.cs`

```
using StringReversal.Services;
using Xunit;

namespace StringReversal.Tests;

public class StringReverserTests
{
    private readonly StringReverser _reverser;

    public StringReverserTests()
    {
        _reverser = new StringReverser();
    }

    [Theory]
    [InlineData("hello", "olleh")]
    [InlineData("world", "dlrow")]
    [InlineData("C#", "#C")]
    [InlineData("12345", "54321")]
    [InlineData("A", "A")]
    [InlineData("", "")]
    [InlineData("racecar", "racecar")]
    public void ReverseUsingArrayReverse_ShouldReverseCorrectly(string input, string expected)
    {
        // Act
        var result = _reverser.ReverseUsingArrayReverse(input);

        // Assert
        Assert.Equal(expected, result);
    }

    [Theory]
    [InlineData("hello", "olleh")]
    [InlineData("world", "dlrow")]
    public void ReverseUsingLinq_ShouldReverseCorrectly(string input, string expected)
    {
        // Act
        var result = _reverser.ReverseUsingLinq(input);

        // Assert
        Assert.Equal(expected, result);
    }

    [Fact]
    public void ReverseUsingLoop_NullInput_ShouldReturnNull()
    {
        // Arrange
        string? input = null;

        // Act
        var result = _reverser.ReverseUsingLoop(input);

        // Assert
        Assert.Null(result);
    }
}
```

### Run Tests
```
dotnet test
```

---

## Performance Comparison

| Method | Time Complexity | Space Complexity | Best For |
|--------|----------------|------------------|----------|
| Array.Reverse | O(n) | O(n) | General purpose, readable |
| LINQ Reverse | O(n) | O(n) | Functional style, concise |
| Manual Loop | O(n) | O(n) | Learning, full control |
| StringBuilder | O(n) | O(n) | Large strings, performance |
| Span<T> | O(n) | O(1) | Memory efficiency, modern C# |

---

## Edge Cases to Handle

1. **Null input** → Return null or empty string
2. **Empty string** → Return empty string
3. **Single character** → Return same character
4. **Palindromes** → Should equal original (e.g., "racecar")
5. **Special characters** → Should be preserved (e.g., "Hello!" → "!olleH")
6. **Unicode characters** → Handle correctly with .NET string methods

---

## Key Learning Points

- Multiple approaches to solve the same problem  
- Understanding time and space complexity  
- Using modern C# features (Span<T>)  
- Extension methods for cleaner code  
- Unit testing best practices  
- Edge case handling  
- Performance considerations

---

## Workflow

```
graph TD
    A[Start] --> B[Get Input String]
    B --> C{Is Null/Empty?}
    C -->|Yes| D[Return Input]
    C -->|No| E[Convert to Char Array]
    E --> F[Reverse Array]
    F --> G[Convert Back to String]
    G --> H[Return Result]
    H --> I[End]
```

---

## Expected Output

```
=== String Reversal Program ===

Testing different reversal methods:

Original: 'hello'
Array.Reverse: 'olleh'
LINQ: 'olleh'
Loop: 'olleh'
StringBuilder: 'olleh'
Span<T>: 'olleh'
Extension: 'olleh'
--------------------------------------------------

Original: 'world'
Array.Reverse: 'dlrow'
LINQ: 'dlrow'

```

---

## Extensions & Challenges

### Beginner Level
- Add support for ignoring spaces
- Reverse only words (not entire string)
- Case-insensitive reversal

### Intermediate Level
- Reverse string in-place without creating new array
- Benchmark all methods and compare performance
- Add async/await version for file-based input

### Advanced Level
- Implement using recursion
- Handle multi-byte Unicode characters correctly
- Create a microservice API endpoint for string reversal

---

## Summary

This task demonstrates fundamental string manipulation in .NET Core using multiple approaches. You'll learn about:
- Basic C# string operations
- Performance optimization techniques
- Modern C# features (Span<T>)
- Unit testing practices
- Edge case handling

**Completion Checklist:**
- [ ] Implement at least 3 reversal methods
- [ ] Handle edge cases (null, empty, single char)
- [ ] Write unit tests
- [ ] Test with various inputs
- [ ] Document your code
- [ ] Compare performance (optional)

---

**Created for:** .NET Core String Manipulation Practice  
**Difficulty:** Beginner  
**Estimated Time:** 30 minutes  
**Last Updated:** November 2025
