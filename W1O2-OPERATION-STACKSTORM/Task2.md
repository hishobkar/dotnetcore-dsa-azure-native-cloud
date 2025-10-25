# Task 2: Create a Basic Stack Using Array (.NET Core)

## Objective
In this task, you will:
1. Create a **custom Stack implementation** using a **fixed-size array**  
2. Implement core stack operations:
- `Push()` – Add element  
- `Pop()` – Remove and return top element  
- `Peek()` – View the top element without removing  
- `IsEmpty()` – Check if stack is empty  

-> Understand how stack logic is implemented internally (without using `Stack<T>`)

---

## Estimated Time: *45 minutes*

---

## Key Concept Recap (LIFO - Last In, First Out)
A stack works like a stack of books :  
- The **last book placed** (Push) is the **first one removed** (Pop).  
- Only the **top element** can be accessed or removed at any time.

---

## Stack Structure (Array-Based)

| Component | Description |
|-----------|-------------|
| Array | Used to store elements |
| Top | Tracks the current top index |
| MaxSize | Fixed size of the stack |
| Push | Adds an element (only if not full) |
| Pop | Removes and returns the top element |
| Peek | Returns the top, without removing |
| IsEmpty | Returns true if stack has no elements |

---

## Implementation (.NET Core / C#)

```csharp
using System;

class ArrayStack
{
    private int[] stack; // Array to hold stack elements
    private int top;     // Index of the top element
    private int maxSize; // Maximum stack size

    public ArrayStack(int size)
    {
        maxSize = size;
        stack = new int[maxSize];
        top = -1; // Indicates stack is empty initially
    }

    // Push method: Adds element on top
    public void Push(int value)
    {
        if (top == maxSize - 1)
        {
            Console.WriteLine("Stack Overflow! Cannot add more elements.");
            return;
        }
        stack[++top] = value;
        Console.WriteLine($"{value} pushed into stack.");
    }

    // Pop method: Removes and returns top element
    public int Pop()
    {
        if (IsEmpty())
        {
            Console.WriteLine("Stack Underflow! No elements to remove.");
            return -1; // Indicating failure
        }
        return stack[top--];
    }

    // Peek method: Returns top element without removing
    public int Peek()
    {
        if (IsEmpty())
        {
            Console.WriteLine("Stack is empty.");
            return -1;
        }
        return stack[top];
    }

    // Check if stack is empty
    public bool IsEmpty()
    {
        return top == -1;
    }

    // Display stack elements (Optional helper)
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
        ArrayStack myStack = new ArrayStack(5);

        myStack.Push(10);
        myStack.Push(20);
        myStack.Push(30);

        myStack.Display();

        Console.WriteLine($"Top element (Peek): {myStack.Peek()}");

        Console.WriteLine($"Popped: {myStack.Pop()}");
        Console.WriteLine($"Popped: {myStack.Pop()}");

        myStack.Display();

        Console.WriteLine($"Is stack empty? {myStack.IsEmpty()}");
    }
}
