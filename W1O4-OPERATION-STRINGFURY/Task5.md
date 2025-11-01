# Task 5: Refactor and Optimize Solutions - .NET Core

## Problem Statement
Refactor previous string manipulation solutions for maximum code readability and performance. Optionally, compare iterative and recursive approaches where applicable.

---

## Objective
- Refactor Reverse String, Palindrome Check, and Character Frequency code.
- Adopt best practices for code layout, naming, and documentation.
- Optimize for speed and memory usage.
- Optionally, show both iterative and recursive methods for reversing and palindrome checking.

---

## Estimated Time **30 minutes**

---

## Tech Stack
- **.NET Core** (C#)
- **Console Application**
- **xUnit** (optional, for unit testing)

---

## Refactoring Guidelines

- Use meaningful class and method names.
- Add XML documentation comments.
- Modularize code: keep logic concise and separated.
- Use efficient algorithms (prefer O(n)).
- Avoid unnecessary allocations.  
- Use modern C# features (expression-bodied members, `Span<T>`, pattern matching).

---

## Refactored Solutions

### 1. Reverse String

#### Iterative Approach (Efficient & Simple)

```
/// <summary>
/// Returns the reverse of the input string. Optimized for performance.
/// </summary>
public static string ReverseIterative(string input)
{
    if (string.IsNullOrEmpty(input)) return input;

    char[] chars = input.ToCharArray();
    int left = 0, right = chars.Length - 1;
    while (left < right)
    {
        (chars[left], chars[right]) = (chars[right], chars[left]);
        left++; right--;
    }
    return new string(chars);
}
```

#### Recursive Approach (Demonstrative)

```
/// <summary>
/// Returns the reversed string using recursion. Not recommended for very large strings.
/// </summary>
public static string ReverseRecursive(string input)
{
    if (string.IsNullOrEmpty(input) || input.Length == 1)
        return input;
    return ReverseRecursive(input.Substring(1)) + input;
}
```

---

### 2. Palindrome Check

#### Iterative (Two-Pointer Technique)

```
/// <summary>
/// Checks if the input string is a palindrome. Ignores case and can ignore non-alphanumeric characters.
/// </summary>
public static bool IsPalindromeIterative(string input, bool ignoreNonAlphanumeric = true)
{
    if (input == null) return true;

    int left = 0, right = input.Length - 1;
    while (left < right)
    {
        char a = input[left], b = input[right];
        if (ignoreNonAlphanumeric)
        {
            if (!char.IsLetterOrDigit(a)) { left++; continue; }
            if (!char.IsLetterOrDigit(b)) { right--; continue; }
        }

        if (char.ToLowerInvariant(a) != char.ToLowerInvariant(b)) return false;
        left++; right--;
    }
    return true;
}
```

#### Recursive (For Learning)

```
/// <summary>
/// Recursively checks if a string is a palindrome. Ignores case/non-alphanumeric if enabled.
/// </summary>
public static bool IsPalindromeRecursive(string input, int left, int right, bool ignoreNonAlphanumeric = true)
{
    if (left >= right) return true;
    char a = input[left], b = input[right];
    if (ignoreNonAlphanumeric)
    {
        if (!char.IsLetterOrDigit(a)) return IsPalindromeRecursive(input, left + 1, right, ignoreNonAlphanumeric);
        if (!char.IsLetterOrDigit(b)) return IsPalindromeRecursive(input, left, right - 1, ignoreNonAlphanumeric);
    }
    if (char.ToLowerInvariant(a) != char.ToLowerInvariant(b)) return false;
    return IsPalindromeRecursive(input, left + 1, right - 1, ignoreNonAlphanumeric);
}
```
**Usage:**  
`IsPalindromeRecursive(input, 0, input.Length - 1, true);`

---

### 3. Character Frequency (Optimized)

```
/// <summary>
/// Returns character frequency count. Optionally normalize case.
/// </summary>
public static Dictionary<char, int> GetCharacterFrequency(string input, bool ignoreCase = false)
{
    var freq = new Dictionary<char, int>();
    if (string.IsNullOrEmpty(input)) return freq;

    foreach (char ch in input)
    {
        char key = ignoreCase ? char.ToLowerInvariant(ch) : ch;
        freq[key] = freq.TryGetValue(key, out int val) ? val + 1 : 1;
    }
    return freq;
}
```

---

## Performance Comparison Table

| Problem         | Iterative | Recursive | Comments                     |
|-----------------|-----------|-----------|------------------------------|
| Reverse String  | O(n), fast| O(n²)*    | Iterative better for size    |
| Palindrome      | O(n), fast| O(n)      | Recursive has stack overhead |
| Char Frequency  | O(n)      | N/A       | Dict is optimal              |

\* Recursion with string concatenation causes O(n²) due to substring/copy overhead.

---

## Example Usage

```
// Reverse
Console.WriteLine(ReverseIterative("hello")); // "olleh"
Console.WriteLine(ReverseRecursive("hello")); // "olleh"

// Palindrome
Console.WriteLine(IsPalindromeIterative("A man a plan a canal Panama")); // true
Console.WriteLine(IsPalindromeRecursive("Racecar", 0, 6)); // true

// Char Frequency
var freq = GetCharacterFrequency("Hello, World!", true);
// Output: {'h':1, 'e':1, 'l':3, 'o':2, ',':1, ' ':1, 'w':1, 'r':1, 'd':1, '!':1}
```

---

## Refactoring Notes

- Iterative is preferred for production (efficient, clear).
- Recursive methods are useful for educational demos, not large strings.
- Use XML comments for all public methods.
- Always modularize: utilities go in static classes; model and logic in services.
- Prefer expression-bodied members when concise.
- Prefer using statements and modern C# features for brevity and safety.

---

## Completion Checklist

- [ ] Refactored all methods: concise, readable, documented.
- [ ] Compared iterative vs. recursive approaches.
- [ ] Ensured performance optimizations.
- [ ] Provided sample code and usage examples.
- [ ] Updated testing and documentation.
