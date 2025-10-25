# Task 2: Traversing & Printing Arrays (.NET Core)

## Objective
Write a .NET Core console application that:
1. Creates an array  
2. Iterates through it using `for`, `foreach`, and `while` loops  
3. Displays all elements during each traversal method  

---

## Estimated Time: *30 minutes*

---

## Task Requirements

1. Create an **int array** with at least 5 values.
2. Print all elements using:
   - `for` loop  
   - `foreach` loop  
   - `while` loop  
3. Clearly label each output section.
4. Ensure clean and readable output formatting.

---

## Implementation (.NET Core / C#)

```csharp
using System;

class Program
{
    static void Main()
    {
        // Step 1: Declare and initialize an integer array
        int[] numbers = { 10, 20, 30, 40, 50 };

        // Step 2: Traverse using for loop
        Console.WriteLine("=== Traversing using for loop ===");
        for (int i = 0; i < numbers.Length; i++)
        {
            Console.WriteLine($"Element at index {i}: {numbers[i]}");
        }

        Console.WriteLine();

        // Step 3: Traverse using foreach loop
        Console.WriteLine("=== Traversing using foreach loop ===");
        foreach (int num in numbers)
        {
            Console.WriteLine($"Element: {num}");
        }

        Console.WriteLine();

        // Step 4: Traverse using while loop
        Console.WriteLine("=== Traversing using while loop ===");
        int index = 0;
        while (index < numbers.Length)
        {
            Console.WriteLine($"Element at index {index}: {numbers[index]}");
            index++;
        }

        Console.WriteLine("\nTraversal complete.");
    }
}
