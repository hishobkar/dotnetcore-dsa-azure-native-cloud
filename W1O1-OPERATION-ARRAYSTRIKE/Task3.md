# Task 3: Basic Array Operations (Insert, Update, Delete) – .NET Core

## Objective
Simulate basic array operations in a .NET Core console application:
1. Insert an element into a specific index (by shifting elements)  
2. Update an existing element by index  
3. Delete an element (by removing value and shifting remaining elements)  

⚠ Since arrays in C# are fixed in size, insertion and deletion should be handled logically by shifting elements rather than physically resizing the array.

---

## Estimated Time: *45 minutes*

---

## Task Requirements

### 1️. Insertion (Logic-Based)
- Create an integer array with a fixed size (e.g., 6).
- Initially fill only part of it (e.g., first 4 elements).
- Ask the user for:
  - A new element
  - The index at which to insert
- Shift elements to the right to make space and insert the new element.

### 2️. Update
- Ask the user for:
  - Index to update
  - New value
- Replace the element at the given index.

### 3️. Deletion (Logic-Based)
- Ask the user for an index to delete.
- Shift all elements after that index to the left.
- Optionally, set the last element to 0 or a default value.

---

## Implementation (C# / .NET Core)

```csharp
using System;

class Program
{
    static void Main()
    {
        // Step 1: Initial array setup
        int[] numbers = new int[6] { 10, 20, 30, 40, 0, 0 };
        int currentLength = 4; // array currently has 4 filled elements

        void PrintArray()
        {
            Console.WriteLine("Current Array: ");
            for (int i = 0; i < numbers.Length; i++)
            {
                Console.Write(numbers[i] + " ");
            }
            Console.WriteLine("\n");
        }

        Console.WriteLine("=== Initial Array ===");
        PrintArray();

        // Step 2: Insert an element
        Console.Write("Enter a number to insert: ");
        int insertValue = Convert.ToInt32(Console.ReadLine());
        Console.Write("Enter index to insert at (0 to " + currentLength + "): ");
        int insertIndex = Convert.ToInt32(Console.ReadLine());

        if (insertIndex >= 0 && insertIndex <= currentLength && currentLength < numbers.Length)
        {
            for (int i = currentLength; i > insertIndex; i--)
            {
                numbers[i] = numbers[i - 1];
            }
            numbers[insertIndex] = insertValue;
            currentLength++;
            Console.WriteLine("After Insertion:");
            PrintArray();
        }
        else
        {
            Console.WriteLine("Invalid index or array is full.");
        }

        // Step 3: Update an element
        Console.Write("Enter index to update (0 to " + (currentLength - 1) + "): ");
        int updateIndex = Convert.ToInt32(Console.ReadLine());
        Console.Write("Enter new value: ");
        int updateValue = Convert.ToInt32(Console.ReadLine());

        if (updateIndex >= 0 && updateIndex < currentLength)
        {
            numbers[updateIndex] = updateValue;
            Console.WriteLine("After Update:");
            PrintArray();
        }
        else
        {
            Console.WriteLine("Invalid update index.");
        }

        // Step 4: Delete an element
        Console.Write("Enter index to delete (0 to " + (currentLength - 1) + "): ");
        int deleteIndex = Convert.ToInt32(Console.ReadLine());

        if (deleteIndex >= 0 && deleteIndex < currentLength)
        {
            for (int i = deleteIndex; i < currentLength - 1; i++)
            {
                numbers[i] = numbers[i + 1];
            }
            numbers[currentLength - 1] = 0; // optional cleanup
            currentLength--;
            Console.WriteLine("After Deletion:");
            PrintArray();
        }
        else
        {
            Console.WriteLine("Invalid delete index.");
        }
    }
}
