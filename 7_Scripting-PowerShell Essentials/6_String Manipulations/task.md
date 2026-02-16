# String Manipulations - Task

## Goals

- Learn string manipulation techniques in PowerShell
- Work with character arrays and sorting
- Implement case conversion logic

## Tasks

1. Create a new sorted string using two strings with random characters in them.
   Strings contains only letters from a to z.

   **Example:**

   ```powershell
   $str1 = "xyaabbbccccdefww"
   $str2 = "xxxxyyyyabklmopq"
   ```

   **Result:**

   ```
   "abcdefklmopqwxy"
   ```

2. Write a script that converts dash/underscore delimited words into camel and pascal casing.

   **Example 1:**

   ```powershell
   $inputString = "the-stealth-warrior"
   # Result: "theStealthWarrior"
   ```

   **Example 2:**

   ```powershell
   $inputString = "The_Stealth_Warrior"
   # Result: "TheStealthWarrior"
   ```

## Self-Review

- **Task 1:** Sorted unique characters from both strings
- **Task 2:** Dash/underscore words converted to camelCase/PascalCase

## Helpful Materials

- https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_split
- https://docs.microsoft.com/en-us/dotnet/api/system.string
