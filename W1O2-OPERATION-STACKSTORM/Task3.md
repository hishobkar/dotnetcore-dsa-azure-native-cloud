# Task 3: Stack Implementation Using Generics (.NET Core)

## Objective
In this task, you will:
1. Refactor your custom stack implementation to use **generics (`Stack<T>`)**  
2. Enable the stack to handle **multiple data types** (int, string, double, etc.)  
3. Implement core stack operations:
- `Push(T item)` – Add element  
- `Pop()` – Remove and return top element  
- `Peek()` – View the top element without removing  
- `IsEmpty()` – Check if stack is empty  

---

## Estimated Time: *45 minutes*

---

## Why Use Generics?
Generics allow you to create a **type-safe stack** that works with **any data type** without rewriting code for each type.

Advantages:
- Type safety (no casting required)  
- Code reusability  
- Reduced errors and improved maintainability  

---

## Generic Stack Implementation (.NET Core / C#)

```csharp
using System;

class GenericStack<T>
{
    private T[] stack;
    private int top;
    private int maxSize;

    public GenericStack(int size)
    {
        maxSize = size;
        stack = new T[maxSize];
        top = -1;   // represents the stack is empty
    }

    // Push method
    public void Push(T value)
    {
        if (top == maxSize - 1)
        {
            Console.WriteLine("Stack Overflow! Cannot add more elements.");
            return;
        }
        stack[++top] = value;
        Console.WriteLine($"{value} pushed into stack.");
    }

    // Pop method
    public T Pop()
    {
        if (IsEmpty())
        {
            Console.WriteLine("Stack Underflow! No elements to remove.");
            return default(T); // Return default value for type T
        }
        return stack[top--];
    }

    // Peek method
    public T Peek()
    {
        if (IsEmpty())
        {
            Console.WriteLine("Stack is empty.");
            return default(T);
        }
        return stack[top];
    }

    // Check if stack is empty
    public bool IsEmpty()
    {
        return top == -1;
    }

    // Display stack elements
    public void Display()
    {
        if (IsEmpty())
        {
            Console.WriteLine("Stack is empty.");
            return;
        }

        Console.WriteLine("Stack elements from top to bottom:");
        for (int i = top; i >= 0; i--)
        {
            Console.WriteLine(stack[i]);
        }
    }
}

// Test Program
class Program
{
    static void Main()
    {
        // Integer Stack
        GenericStack<int> intStack = new GenericStack<int>(5);
        intStack.Push(10);
        intStack.Push(20);
        intStack.Display();

        // String Stack
        GenericStack<string> stringStack = new GenericStack<string>(3);
        stringStack.Push("Apple");
        stringStack.Push("Banana");
        stringStack.Display();

        // Peek and Pop
        Console.WriteLine($"Top of int stack: {intStack.Peek()}");
        Console.WriteLine($"Popped from string stack: {stringStack.Pop()}");
        stringStack.Display();
    }
}
