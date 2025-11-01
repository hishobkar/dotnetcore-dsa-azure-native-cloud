# Task 4: Enhance Previous Problems with Edge Case Handling - .NET Core Guide

## Problem Statement
Augment the solutions for the earlier string tasks (Reverse String, Check Palindrome, Count Character Frequency) to include robust validation and comprehensive edge case handling.

---

## Objective
Integrate logic into previously created methods to handle and validate:
- Empty strings
- Strings with only spaces
- Special characters
- Case sensitivity (as per context)
- Null values

---

## Estimated Time **45 minutes**

---

## Tech Stack
- **.NET Core** (C#)
- **Console Application** or **Minimal API**
- **xUnit** (optional)

---

## Prerequisites
- .NET Core SDK 9.0 (or .NET 6/7/8)
- Visual Studio 2022 / VS Code / Rider

---

## Edge Case Checklist

| Case                      | Reverse String | Palindrome Check | Character Frequency |
|---------------------------|:--------------:|:----------------:|:------------------:|
| Null input                | ✓              | ✓                | ✓                  |
| Empty string `""`         | ✓              | ✓                | ✓                  |
| Whitespace-only `"   "`   | ✓              | ✓                | ✓                  |
| Special characters `"@!"` | ✓              | ✓                | ✓                  |
| Spaces `"a b"`            | ✓              | ✓                | ✓                  |
| Case sensitivity          | Option         | ✓ (ignore case)  | Option             |

---

## Implementation Enhancements

### Reverse String

**Improved Method:**

```
public static string Reverse(string input)
{
    // Null validation
    if (input == null)
        return null;
    // Empty & whitespace handling
    if (input.Trim().Length == 0)
        return input; // Return as-is for empty or whitespace-only

    var charArray = input.ToCharArray();
    Array.Reverse(charArray);
    return new string(charArray);
}
```

- Returns `null` for `null` input.
- Returns as-is for empty or whitespace-only strings.
- Preserves spaces and special characters.

---

### Palindrome Check

**Improved Method:**

```
public static bool IsPalindrome(string input, bool ignoreNonAlphanumeric = false)
{
    if (string.IsNullOrEmpty(input))
        return true; // Treat empty/null as palindrome

    // Normalize case
    input = input.ToLowerInvariant();

    if (ignoreNonAlphanumeric)
    {
        input = new string(input.Where(char.IsLetterOrDigit).ToArray());
    }

    int left = 0, right = input.Length - 1;
    while (left < right)
    {
        if (input[left] != input[right])
            return false;
        left++;
        right--;
    }
    return true;
}
```

- Handles `null`, empty, and whitespace-only input.
- Optionally ignores non-alphanumerics.
- Always ignores case.
- Robust to special characters and spacing.

---

### Count Character Frequency

**Improved Method:**

```
public static Dictionary<char, int> CharacterFrequency(string input, bool ignoreCase = false)
{
    var frequency = new Dictionary<char, int>();
    if (string.IsNullOrEmpty(input))
        return frequency;

    foreach (var ch in input)
    {
        char key = ignoreCase ? char.ToLowerInvariant(ch) : ch;
        if (frequency.ContainsKey(key))
            frequency[key]++;
        else
            frequency[key] = 1;
    }
    return frequency;
}
```

- Returns empty dictionary for `null` or empty input.
- Counts spaces and special characters explicitly.
- Optional case-insensitive frequency (set `ignoreCase=true`).

---

## Sample Edge Case Test Inputs

| Input              | Reverse Output      | Palindrome Output         | Frequency Output                |
|--------------------|--------------------|---------------------------|---------------------------------|
| `null`             | `null`             | `true`                    | `{}`                            |
| `""`               | `""`               | `true`                    | `{}`                            |
| `"   "`            | `"   "`            | `true`                    | `{' ': 3}`                      |
| `"A man, a plan "` | `" nalp a ,nam A"` | Depends on options        | Counts normal and special chars |
| `"@!@!@!"`         | `"!@!@!@"`         | `false`                   | `{'@': 3, '!': 3}`              |
| `"Racecar"`        | `"racecaR"`        | `true` (ignore case)      | `{R: 1, a: 2, c: 2, e: 1, r: 1}`|

---

## Edge Case Testing: Example Code Snippets

**Unit Tests Example:**
```
using Xunit;

// Reverse
Assert.Null(Reverse(null));
Assert.Equal("", Reverse(""));
Assert.Equal("   ", Reverse("   "));
Assert.Equal("!dlrow olleh", Reverse("hello world!"));

// Palindrome
Assert.True(IsPalindrome(null));
Assert.True(IsPalindrome(""));
Assert.True(IsPalindrome("   "));
Assert.True(IsPalindrome("A man a plan a canal Panama", true));
Assert.False(IsPalindrome("@!@!@!"));

// Frequency
var freq = CharacterFrequency(null); // Empty dictionary
freq = CharacterFrequency("AaA", ignoreCase: true); // {'a': 3}
freq = CharacterFrequency("AaA", ignoreCase: false); // {'A': 2, 'a': 1}
```

---

## Best Practices

1. Validate input (`null`, whitespace, empty)
2. Allow customization for case and character filtering
3. Test with variety of edge cases
4. Document assumptions and options in method signatures
5. Include unit tests for all edge cases

---

## Extensions & Challenges

- Extend reversal for Unicode-aware (surrogate pairs)
- Palindrome detection using normalization for phrases/sentences
- Group character frequency by category (letters, digits, special)

---

## Completion Checklist

- [ ] Enhanced methods implemented
- [ ] Edge cases tested and handled
- [ ] Input validation robust
- [ ] Sample unit tests written
- [ ] Documentation updated

---

## Summary

This enhancement task strengthens all prior solutions by validating and handling key edge cases—ensuring each string manipulation method behaves reliably under all realistic scenarios in .NET Core.
