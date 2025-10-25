# Task 4: Exception Handling & Edge Cases for Stack (.NET Core)

## Objective
In this task, you will:
1. Add **exception handling** for stack overflow and underflow scenarios  
2. Ensure your stack methods (`Push`, `Pop`, `Peek`) handle invalid operations gracefully  
3. Write **test cases** to validate each method for correct behavior  

---

## Estimated Time: *45 minutes*

---

## Key Concepts

### Stack Exceptions
- **Overflow**: Trying to push an element when the stack is full  
- **Underflow**: Trying to pop or peek when the stack is empty  

### Handling Exceptions in C#
- Use `try-catch` blocks to catch exceptions  
- Optionally, create **custom exception classes** for more clarity  

---

## Enhanced Stack Implementation with Exception Handling (.NET Core / C#)

```csharp
using System;

// Custom exception for Stack Overflow
public class StackOverflowException : Exception
{
    public StackOverflowException(string message) : base(message) { }
}

// Custom exception for Stack Underflow
public class StackUnderflowException : Exception
{
    public StackUnderflowException(string message) : base(message) { }
}

class SafeStack<T>
{
    private T[] stack;
    private int top;
    private int maxSize;

    public SafeStack(int size)
    {
        maxSize = size;
        stack = new T[maxSize];
        top = -1;
    }

    public void Push(T value)
    {
        if (top == maxSize - 1)
        {
            throw new StackOverflowException("Cannot push: Stack is full.");
        }
        stack[++top] = value;
    }

    public T Pop()
    {
        if (IsEmpty())
        {
            throw new StackUnderflowException("Cannot pop: Stack is empty.");
        }
        return stack[top--];
    }

    public T Peek()
    {
        if (IsEmpty())
        {
            throw new StackUnderflowException("Cannot peek: Stack is empty.");
        }
        return stack[top];
    }

    public bool IsEmpty()
    {
        return top == -1;
    }
}

class Program
{
    static void Main()
    {
        SafeStack<int> stack = new SafeStack<int>(3);

        // Test Cases
        try
        {
            Console.WriteLine("Pushing 10");
            stack.Push(10);
            Console.WriteLine("Pushing 20");
            stack.Push(20);
            Console.WriteLine("Pushing 30");
            stack.Push(30);

            Console.WriteLine("Attempting to push 40 (should cause overflow)");
            stack.Push(40); // This will throw exception
        }
        catch (StackOverflowException ex)
        {
            Console.WriteLine(ex.Message);
        }

        try
        {
            Console.WriteLine($"\nPeek top element: {stack.Peek()}");
            Console.WriteLine($"Popped: {stack.Pop()}");
            Console.WriteLine($"Popped: {stack.Pop()}");
            Console.WriteLine($"Popped: {stack.Pop()}");

            Console.WriteLine("Attempting to pop from empty stack (should cause underflow)");
            stack.Pop(); // This will throw exception
        }
        catch (StackUnderflowException ex)
        {
            Console.WriteLine(ex.Message);
        }

        // Edge Case: Check IsEmpty
        Console.WriteLine($"\nIs stack empty? {stack.IsEmpty()}");
    }
}
