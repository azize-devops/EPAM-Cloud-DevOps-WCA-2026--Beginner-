# Object Properties and Methods - Task

## Goals

- Learn to create PowerShell classes
- Implement custom methods in classes
- Work with object properties

## Tasks

1. Write a PowerShell class to implement `pow(x, n)` that returns the base to the exponent power (base^exponent).

   **Example 1:**

   ```powershell
   $MyCustomObject.Pow(2, 3)
   # Returns: 8
   ```

   **Example 2:**

   ```powershell
   $MyCustomObject.Pow(7, 2)
   # Returns: 49
   ```

2. Write a PowerShell class which has two methods `SetString()` and `PrintString()`.
   `SetString()` accepts a string from the user and `PrintString()` prints the string in upper case.

   **Example 1:**

   ```powershell
   $MyCustomObject.SetString("You shall not pass!")
   ```

   **Example 2:**

   ```powershell
   $MyCustomObject.PrintString()
   # Returns: YOU SHALL NOT PASS!
   ```

3. Write a PowerShell class to reverse a string word by word.

   **Example:**

   ```powershell
   $MyCustomObject.Reverse("Nostromo")
   # Returns: omortsoN
   ```

## Self-Review

- **Task 1:** `Pow()` method correctly calculates power
- **Task 2:** `SetString()` stores value, `PrintString()` outputs uppercase
- **Task 3:** `Reverse()` method returns reversed string

## Helpful Materials

- https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_classes
- https://docs.microsoft.com/en-us/powershell/scripting/lang-spec/chapter-09
