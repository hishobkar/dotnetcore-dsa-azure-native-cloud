# Task 5: Multi-Dimensional & Jagged Arrays (.NET Core)

## Objective
Create and work with:
1. A **2D array (multi-dimensional array)**  
2. A **jagged array (array of arrays)**  
Then practice:
- Accessing elements  
- Updating specific elements  
- Printing all values in a structured format  

---

## Estimated Time: *30 minutes*

---

## Task Requirements

### Part A: Multi-Dimensional (2D) Array
1. Declare a **2D integer array** (e.g., 3x3).
2. Initialize it with values.
3. Print all elements using nested loops.
4. Update a value at a specific row and column (prompt user or do hardcoded).
5. Display the updated array.

### Part B: Jagged Array (Array of Arrays)
1. Declare a **jagged array** (e.g., 3 rows, each with a different number of columns).
2. Initialize with sample numbers.
3. Print all values using nested loops.
4. Update one inner element.
5. Display the updated jagged array.

---

## Implementation (.NET Core / C#)

```csharp
using System;

class Program
{
    static void Main()
    {
        // ================================
        // PART A: Multi-Dimensional (2D) Array
        // ================================
        int[,] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };

        Console.WriteLine("=== 2D Array (Before Update) ===");
        Print2DArray(matrix);

        // Update element
        Console.Write("Enter row index (0-2) to update: ");
        int row = Convert.ToInt32(Console.ReadLine());
        Console.Write("Enter column index (0-2) to update: ");
        int col = Convert.ToInt32(Console.ReadLine());
        Console.Write("Enter new value: ");
        int newValue = Convert.ToInt32(Console.ReadLine());

        if (row >= 0 && row < 3 && col >= 0 && col < 3)
        {
            matrix[row, col] = newValue;
            Console.WriteLine("=== 2D Array (After Update) ===");
            Print2DArray(matrix);
        }
        else
        {
            Console.WriteLine("Invalid row or column index.");
        }

        // ================================
        // PART B: Jagged Array
        // ================================
        int[][] jaggedArr = new int[3][];
        jaggedArr[0] = new int[] { 10, 20 };
        jaggedArr[1] = new int[] { 30, 40, 50 };
        jaggedArr[2] = new int[] { 60 };

        Console.WriteLine("\n=== Jagged Array (Before Update) ===");
        PrintJaggedArray(jaggedArr);

        // Update jagged array element
        Console.Write("Enter row index (0-2) for jagged array: ");
        row = Convert.ToInt32(Console.ReadLine());
        Console.Write($"Enter column index (0-{jaggedArr[row].Length - 1}) for that row: ");
        col = Convert.ToInt32(Console.ReadLine());
        Console.Write("Enter new value: ");
        newValue = Convert.ToInt32(Console.ReadLine());

        if (row >= 0 && row < jaggedArr.Length && col >= 0 && col < jaggedArr[row].Length)
        {
            jaggedArr[row][col] = newValue;
            Console.WriteLine("\n=== Jagged Array (After Update) ===");
            PrintJaggedArray(jaggedArr);
        }
        else
        {
            Console.WriteLine("Invalid row/column index for jagged array.");
        }
    }

    // Helper method to print 2D array
    static void Print2DArray(int[,] arr)
    {
        for (int i = 0; i < arr.GetLength(0); i++)
        {
            for (int j = 0; j < arr.GetLength(1); j++)
            {
                Console.Write(arr[i, j] + " ");
            }
            Console.WriteLine();
        }
    }

    // Helper method to print jagged array
    static void PrintJaggedArray(int[][] arr)
    {
        for (int i = 0; i < arr.Length; i++)
        {
            for (int j = 0; j < arr[i].Length; j++)
            {
                Console.Write(arr[i][j] + " ");
            }
            Console.WriteLine();
        }
    }
}
