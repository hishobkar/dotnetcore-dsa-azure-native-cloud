# Task 5: Comparison with Built-in `.NET Stack<T>` (.NET Core)

## Objective
In this task, you will:
1. Use .NET’s built-in **`Stack<T>`** class in a sample application  
2. Compare its **performance and features** with a **custom stack implementation**  
3. Understand **advantages of using built-in collections** versus manual implementations  

---

## Estimated Time: *30 minutes*

---

## Key Concepts

### Built-in Stack<T>
- Namespace: `System.Collections.Generic`  
- Generic and type-safe  
- Provides efficient implementations of stack operations:
  - `Push(T item)`  
  - `Pop()`  
  - `Peek()`  
  - `Contains(T item)`  
  - `Count` property  

### Comparison Points
| Feature | Custom Stack | .NET Stack<T> |
|---------|-------------|---------------|
| Implementation | Array or List-based | Optimized generic collection |
| Type Safety | Manual with generics | Built-in generic support |
| Overflow/Underflow | Manual checks needed | Throws exceptions automatically |
| Methods Available | Push, Pop, Peek, IsEmpty | Push, Pop, Peek, Contains, Count, ToArray |
| Performance | Depends on implementation | Optimized for speed and memory |

---

## Application Using Built-in Stack<T> (.NET Core / C#)

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;

class Program
{
    static void Main()
    {
        // Using built-in Stack<int>
        Stack<int> builtInStack = new Stack<int>();

        Console.WriteLine("=== Using .NET Stack<T> ===");

        // Push elements
        builtInStack.Push(10);
        builtInStack.Push(20);
        builtInStack.Push(30);

        Console.WriteLine("Stack after pushing elements:");
        foreach (var item in builtInStack)
        {
            Console.WriteLine(item);
        }

        // Peek
        Console.WriteLine($"Top element (Peek): {builtInStack.Peek()}");

        // Pop
        Console.WriteLine($"Popped element: {builtInStack.Pop()}");

        // Display remaining elements
        Console.WriteLine("Stack after popping:");
        foreach (var item in builtInStack)
        {
            Console.WriteLine(item);
        }

        // Check if stack contains a value
        Console.WriteLine($"Contains 20? {builtInStack.Contains(20)}");

        // Compare performance with custom stack
        const int iterations = 1000000;
        Stopwatch stopwatch = new Stopwatch();

        // Test custom stack
        var customStack = new CustomStack<int>(iterations);
        stopwatch.Start();
        for (int i = 0; i < iterations; i++)
        {
            customStack.Push(i);
        }
        stopwatch.Stop();
        Console.WriteLine($"\nCustom stack push {iterations} elements: {stopwatch.ElapsedMilliseconds} ms");

        // Test built-in stack
        stopwatch.Restart();
        var netStack = new Stack<int>(iterations);
        for (int i = 0; i < iterations; i++)
        {
            netStack.Push(i);
        }
        stopwatch.Stop();
        Console.WriteLine($".NET Stack<T> push {iterations} elements: {stopwatch.ElapsedMilliseconds} ms");
    }
}

// Simple CustomStack for comparison
class CustomStack<T>
{
    private T[] stack;
    private int top;

    public CustomStack(int size)
    {
        stack = new T[size];
        top = -1;
    }

    public void Push(T item)
    {
        stack[++top] = item;
    }
}
