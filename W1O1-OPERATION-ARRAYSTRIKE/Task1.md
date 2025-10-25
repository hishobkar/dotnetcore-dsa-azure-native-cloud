# Task 1: Array Creation & Initialization (.NET Core)

## Objective
Create arrays of different data types (`int`, `string`, `double`). Practice initializing them with:

1. Static (hardcoded) values  
2. User input (dynamic initialization)

---

## Estimated Time: *30 minutes*

---

## Part A: Static Initialization (Hardcoded Values)

### Instructions:
1. Create an **int array** with 5 numbers.
2. Create a **string array** with 3 names.
3. Create a **double array** with 4 decimal values.
4. Loop through each array and display the elements.

---

## Part B: Dynamic Initialization (User Input)

### Instructions:
1. Ask the user to enter the number of elements for an int array.
2. Take user input for each element.
3. Display the array after input.

---

## Implementation (.NET Core / C#)

```csharp
using System;

class Program
{
    static void Main()
    {
        // Part A: Static Initialization
        int[] numbers = { 10, 20, 30, 40, 50 };           // int array
        string[] names = { "Alice", "Bob", "Charlie" };   // string array
        double[] prices = { 10.5, 20.75, 30.99, 40.0 };   // double array

        Console.WriteLine("=== Static Arrays ===");
        Console.Write("Numbers: ");
        foreach (int num in numbers)
        {
            Console.Write(num + " ");
        }
        Console.WriteLine();

        Console.Write("Names: ");
        foreach (string name in names)
        {
            Console.Write(name + " ");
        }
        Console.WriteLine();

        Console.Write("Prices: ");
        foreach (double price in prices)
        {
            Console.Write(price + " ");
        }
        Console.WriteLine("\n");

        // Part B: Dynamic Initialization (User Input)
        Console.Write("Enter the number of elements for the int array: ");
        int size = Convert.ToInt32(Console.ReadLine());

        int[] userArray = new int[size];

        Console.WriteLine($"Enter {size} numbers:");
        for (int i = 0; i < size; i++)
        {
            Console.Write($"Element {i + 1}: ");
            userArray[i] = Convert.ToInt32(Console.ReadLine());
        }

        Console.Write("You entered: ");
        foreach (int num in userArray)
        {
            Console.Write(num + " ");
        }

        Console.WriteLine();
    }
}
