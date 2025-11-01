# Task 2: Check for Palindrome - .NET Core Implementation Guide

## Problem Statement
Create a function in .NET Core that checks whether a given string is a palindrome.

**Palindrome Definition:**  
A palindrome is a string that reads the same forwards and backwards (e.g., "racecar", "level", "A man a plan a canal Panama").

---

## Objective
Design and implement an efficient method to verify if a string is a palindrome, addressing edge cases and multiple implementation variations.

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
- Understanding of C# strings and case sensitivity

---

## Architecture

### Project Structure
```
PalindromeChecker/
├── PalindromeChecker.csproj
├── Program.cs
├── Services/
│   └── PalindromeService.cs
├── Extensions/
│   └── StringExtensions.cs
└── Tests/ (optional)
    └── PalindromeServiceTests.cs
```

---

## Implementation Approaches

### Approach 1: Compare Reversed String

```
public static bool IsPalindromeUsingReverse(string input)
{
    if (string.IsNullOrEmpty(input))
        return true;

    string reversed = new string(input.Reverse().ToArray());
    return string.Equals(input, reversed, StringComparison.OrdinalIgnoreCase);
}
```

### Approach 2: Two-Pointer Technique

```
public static bool IsPalindromeUsingTwoPointers(string input)
{
    if (string.IsNullOrEmpty(input))
        return true;

    int left = 0;
    int right = input.Length - 1;
    while (left < right)
    {
        if (char.ToLowerInvariant(input[left]) != char.ToLowerInvariant(input[right]))
            return false;
        left++;
        right--;
    }
    return true;
}
```

### Approach 3: Ignore Non-Alphanumeric Characters (Advanced)

```
public static bool IsPalindromeIgnoreNonAlphaNum(string input)
{
    if (string.IsNullOrEmpty(input))
        return true;

    var filtered = input.Where(char.IsLetterOrDigit)
                        .Select(char.ToLowerInvariant)
                        .ToArray();

    int left = 0, right = filtered.Length - 1;
    while (left < right)
    {
        if (filtered[left] != filtered[right])
            return false;
        left++;
        right--;
    }
    return true;
}
```

---

## Step-by-Step Implementation

### Step 1: Create New .NET Core Console Project
```
dotnet new console -n PalindromeChecker
cd PalindromeChecker
```

### Step 2: Create PalindromeService Class

**File:** `Services/PalindromeService.cs`

```
using System;
using System.Linq;

namespace PalindromeChecker.Services;

public class PalindromeService
{
    public bool IsPalindromeUsingReverse(string input)
    {
        if (string.IsNullOrEmpty(input))
            return true;

        var reversed = new string(input.Reverse().ToArray());
        return string.Equals(input, reversed, StringComparison.OrdinalIgnoreCase);
    }

    public bool IsPalindromeUsingTwoPointers(string input)
    {
        if (string.IsNullOrEmpty(input))
            return true;

        int left = 0, right = input.Length - 1;
        while (left < right)
        {
            if (char.ToLowerInvariant(input[left]) != char.ToLowerInvariant(input[right]))
                return false;
            left++;
            right--;
        }
        return true;
    }

    public bool IsPalindromeIgnoreNonAlphaNum(string input)
    {
        if (string.IsNullOrEmpty(input))
            return true;

        var filtered = input.Where(char.IsLetterOrDigit)
                            .Select(char.ToLowerInvariant)
                            .ToArray();

        int left = 0, right = filtered.Length - 1;
        while (left < right)
        {
            if (filtered[left] != filtered[right])
                return false;
            left++;
            right--;
        }
        return true;
    }
}
```

### Step 3: Create Extension Method (Optional)

**File:** `Extensions/StringExtensions.cs`

```
namespace PalindromeChecker.Extensions;

public static class StringExtensions
{
    public static bool IsPalindrome(this string input)
    {
        if (string.IsNullOrEmpty(input))
            return true;
        
        var reversed = new string(input.Reverse().ToArray());
        return string.Equals(input, reversed, StringComparison.OrdinalIgnoreCase);
    }
}
```

### Step 4: Update Program.cs

**File:** `Program.cs`

```
using PalindromeChecker.Services;
using PalindromeChecker.Extensions;

namespace PalindromeChecker;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("=== Palindrome Checker Program ===\n");

        var service = new PalindromeService();

        // Example test cases
        string[] testCases = 
        {
            "racecar",
            "level",
            "deed",
            "Step on no pets",
            "A man a plan a canal Panama",
            "No lemon, no melon",
            "123321",
            "Was it a car or a cat I saw",
            "Not a palindrome"
        };

        Console.WriteLine("Testing palindrome checker methods:\n");

        foreach (var testCase in testCases)
        {
            Console.WriteLine($"Original: '{testCase}'");
            Console.WriteLine($"IsPalindromeUsingReverse: {service.IsPalindromeUsingReverse(testCase)}");
            Console.WriteLine($"IsPalindromeUsingTwoPointers: {service.IsPalindromeUsingTwoPointers(testCase)}");
            Console.WriteLine($"IsPalindromeIgnoreNonAlphaNum: {service.IsPalindromeIgnoreNonAlphaNum(testCase)}");
            Console.WriteLine($"Extension: {testCase.IsPalindrome()}");
            Console.WriteLine(new string('-', 50));
            Console.WriteLine();
        }

        // Interactive mode
        Console.WriteLine("\n=== Interactive Mode ===");
        Console.WriteLine("Enter a string to check for palindrome (or 'exit' to quit):");

        while (true)
        {
            Console.Write("\nInput: ");
            string? input = Console.ReadLine();

            if (string.IsNullOrEmpty(input) || input.ToLower() == "exit")
                break;

            bool isPalindrome = service.IsPalindromeIgnoreNonAlphaNum(input);
            Console.WriteLine($"Palindrome (ignore non-alphanum): {isPalindrome}");
        }

        Console.WriteLine("\nThank you for using Palindrome Checker!");
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
dotnet new xunit -n PalindromeChecker.Tests
dotnet add PalindromeChecker.Tests reference PalindromeChecker
```

**File:** `PalindromeServiceTests.cs`

```
using PalindromeChecker.Services;
using Xunit;

namespace PalindromeChecker.Tests;

public class PalindromeServiceTests
{
    private readonly PalindromeService _service = new();

    [Theory]
    [InlineData("racecar", true)]
    [InlineData("level", true)]
    [InlineData("deed", true)]
    [InlineData("hello", false)]
    [InlineData("12321", true)]
    [InlineData("Not a palindrome", false)]
    [InlineData("", true)]
    [InlineData(null, true)]
    public void IsPalindromeUsingReverse_BasicTests(string input, bool expected)
    {
        Assert.Equal(expected, _service.IsPalindromeUsingReverse(input));
    }

    [Theory]
    [InlineData("A man a plan a canal Panama", true)]
    [InlineData("No lemon, no melon", true)]
    [InlineData("Step on no pets", true)]
    [InlineData("Was it a car or a cat I saw", true)]
    public void IsPalindromeIgnoreNonAlphaNum_AdvancedTests(string input, bool expected)
    {
        Assert.Equal(expected, _service.IsPalindromeIgnoreNonAlphaNum(input));
    }
}
```

### Run Tests
```
dotnet test
```

---

## Performance Comparison

| Method                      | Time Complexity | Space Complexity | Best For                    |
|-----------------------------|----------------|------------------|-----------------------------|
| Compare Reversed String     | O(n)           | O(n)             | Readable, simple            |
| Two-Pointer Technique       | O(n)           | O(1)             | Space efficient             |
| Ignore Non-Alphanumeric     | O(n)           | O(n)             | Real-world text, robust     |

---

## Edge Cases to Handle

1. **Null input** → Treat as palindrome (return true)
2. **Empty string** → Palindrome
3. **Single character** → Palindrome
4. **Case sensitivity** → Should ignore case
5. **Non-alphanumeric chars** → Optionally ignore for phrase checking
6. **Unicode characters** → Should work with .NET strings

---

## Key Learning Points

✅ Understand palindrome definition and logic  
✅ Multiple validation approaches (reverse, two-pointers)  
✅ Case-insensitive and robust solution  
✅ Use of LINQ and extension methods  
✅ Writing and running automated tests  
✅ Handling edge cases

---

## Workflow

```
graph TD
    A[Start] --> B[Get Input String]
    B --> C{Null/Empty?}
    C -->|Yes| D[Return True]
    C -->|No| E[Sanitize (ignore case and non-alphanum)]
    E --> F[Check equality from both ends]
    F --> G{Matches?}
    G -->|Yes| H[Return True]
    G -->|No| I[Return False]
```

---

## Best Practices

1. ✅ Validate input (null/empty checks)
2. ✅ Ignore case for palindrome check
3. ✅ Write XML comments and clean code
4. ✅ Test with various strings and edge cases
5. ✅ Use xUnit or MSTest for unit testing
6. ✅ Consider performance for large text

---

## Expected Output

```
=== Palindrome Checker Program ===

Original: 'racecar'
IsPalindromeUsingReverse: True
IsPalindromeUsingTwoPointers: True
IsPalindromeIgnoreNonAlphaNum: True
Extension: True
--------------------------------------------------

Original: 'A man a plan a canal Panama'
IsPalindromeUsingReverse: False
IsPalindromeUsingTwoPointers: False
IsPalindromeIgnoreNonAlphaNum: True
Extension: False
...
```

---

## Extensions & Challenges

### Beginner Level
- Add support for phrase palindromes (ignore spaces and punctuation)
- Case-insensitive checking

### Intermediate Level
- Benchmark different methods for performance
- Interactive CLI with validation messages

### Advanced Level
- Create a web API endpoint for palindrome verification
- Unicode normalization for international text
- Display palindrome substrings in a string

---

## Resources

- [Microsoft Docs: String](https://learn.microsoft.com/en-us/dotnet/api/system.string)
- [C# LINQ Reverse](https://learn.microsoft.com/en-us/dotnet/api/system.linq.enumerable.reverse)
- [Unit Testing in .NET](https://learn.microsoft.com/en-us/dotnet/core/testing/)

---

## Summary

This task explores palindrome verification in .NET Core using several approaches. You will create and test functions that efficiently check strings and learn how to handle real-world edge cases and input variations.

**Completion Checklist:**
- [ ] Implement at least 2 palindrome methods
- [ ] Handle edge cases (case, non-alphanum, null)
- [ ] Write and run unit tests
- [ ] Document your code
- [ ] Test with variety of examples

---

**Created for:** .NET Core String Manipulation Practice  
**Difficulty:** Beginner  
**Estimated Time:** 30 minutes  
**Last Updated:** November 2025  
