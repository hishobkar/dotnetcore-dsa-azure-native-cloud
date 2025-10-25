# Task 4: Searching & Sorting Arrays (.NET Core)

## Objective
In this task, you will:
1. Implement **Linear Search** manually  
2. Implement **Binary Search** manually (works only on sorted arrays)  
3. Use the built-in `Array.Sort()` method  
4. Compare manual search results vs built-in sorting and search logic  

---

## Estimated Time: *45 minutes*

---

## Task Requirements

### Step 1: Create an Integer Array
- Hardcode an integer array (e.g., `{34, 12, 56, 78, 23, 90, 11}`).

### Step 2: Implement Linear Search Manually
- Ask the user to enter a number to search.
- Traverse the array element by element.
- Return the index if found, else display "Not found".

### Step 3: Implement Binary Search Manually
- Sort the array first (required for binary search).
- Implement binary search manually using `low`, `high`, and `mid`.
- Return the index if found.

### Step 4: Apply `Array.Sort()` and Display Sorted Array
- Sort the same array using `Array.Sort()`.
- Display sorted array to verify results.

### Step 5: Compare Results
- Explain why binary search only works on sorted arrays.
- Highlight difference between linear vs binary search.

---

## Implementation (.NET Core / C#)

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] numbers = { 34, 12, 56, 78, 23, 90, 11 };

        Console.WriteLine("=== Original Array ===");
        PrintArray(numbers);

        // Step 1: Linear Search
        Console.Write("\nEnter a number to search (Linear Search): ");
        int target = Convert.ToInt32(Console.ReadLine());
        int linearIndex = LinearSearch(numbers, target);

        if (linearIndex != -1)
            Console.WriteLine($"Linear Search: {target} found at index {linearIndex}");
        else
            Console.WriteLine($"Linear Search: {target} not found");

        // Step 2: Manual Binary Search (requires sorted array)
        Array.Sort(numbers); // Sorting before binary search
        Console.WriteLine("\n=== Sorted Array (Before Binary Search) ===");
        PrintArray(numbers);

        Console.Write("\nEnter a number to search (Binary Search): ");
        target = Convert.ToInt32(Console.ReadLine());
        int binaryIndex = BinarySearch(numbers, target);

        if (binaryIndex != -1)
            Console.WriteLine($"Binary Search: {target} found at index {binaryIndex}");
        else
            Console.WriteLine($"Binary Search: {target} not found");
    }

    static int LinearSearch(int[] arr, int target)
    {
        for (int i = 0; i < arr.Length; i++)
        {
            if (arr[i] == target)
                return i;
        }
        return -1;
    }

    static int BinarySearch(int[] arr, int target)
    {
        int low = 0, high = arr.Length - 1;

        while (low <= high)
        {
            int mid = (low + high) / 2;

            if (arr[mid] == target)
                return mid;
            else if (arr[mid] < target)
                low = mid + 1;
            else
                high = mid - 1;
        }
        return -1;
    }

    static void PrintArray(int[] arr)
    {
        foreach (int num in arr)
        {
            Console.Write(num + " ");
        }
        Console.WriteLine();
    }
}
