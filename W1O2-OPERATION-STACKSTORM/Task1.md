# Task 1: Understanding Stack Basics (.NET Core)

## Objective
In this task, you will:
1. Understand the concept of **Stack** as a data structure  
2. Learn the principle of **LIFO (Last In, First Out)**  
3. Explore the built-in **`Stack<T>` class in .NET Core**  
4. Review basic operations such as `Push`, `Pop`, `Peek`, and `Contains`

---

## Estimated Time: *30 minutes*

---

## What is a Stack?
A **Stack** is a linear data structure that follows the **LIFO (Last In, First Out)** principle.

-> This means the **last item added (pushed)** onto the stack is the **first item removed (popped)**.

### Real-Life Example:
- Think of a stack of plates.  
- You place a new plate on top → **Push**  
- You take the top plate off first → **Pop**

---

## Stack Operations (Conceptually)

| Operation | Description |
|-----------|------------|
| `Push(item)` | Adds an item to the top of the stack |
| `Pop()` | Removes and returns the topmost item |
| `Peek()` | Returns the topmost item without removing it |
| `Count` | Gets the total number of items |
| `Contains(item)` | Checks if an item exists in the stack |
| `Clear()` | Removes all items |

---

## Exploring `.NET Core Stack<T>` Class
In .NET Core, Stack is implemented as a generic class:  
```csharp
Stack<T> stackName = new Stack<T>();
