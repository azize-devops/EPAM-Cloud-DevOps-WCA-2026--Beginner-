# STRING MANIPULATIONS - ANSWER

This guide covers PowerShell string manipulation techniques including
sorting, removing duplicates, and case conversion.


## STRING MANIPULATION OVERVIEW

Key concepts:

```powershell
- ToCharArray()          # Convert string to character array
- Sort-Object -Unique    # Sort and remove duplicates
- -join                  # Join array elements into string
- -split                 # Split string by delimiter
- ToUpper() / ToLower()  # Case conversion
- Substring()            # Extract part of string
```


## TASK 1: Create Sorted Unique String from Two Strings

Given strings:

```powershell
$str1 = "xyaabbbccccdefww"
$str2 = "xxxxyyyyabklmopq"
```

> **Screenshot 1**
>
> Show: Define the two input strings
> Expected: Both variables created

Solution - Step by step:

Step 1: Combine the strings

```powershell
$combined = $str1 + $str2
```

> **Screenshot 2**
>
> Show: Combined string
> Expected: "xyaabbbccccdefwwxxxxyyyyabklmopq"

Step 2: Convert to character array

```powershell
$charArray = $combined.ToCharArray()
```

Step 3: Sort and get unique characters

```powershell
$sorted = $charArray | Sort-Object -Unique
```

Step 4: Join back to string

```powershell
$result = -join $sorted
```

> **Screenshot 3**
>
> Show: Step by step execution
> Expected: Each transformation visible

Complete one-liner solution:

```powershell
$str1 = "xyaabbbccccdefww"
$str2 = "xxxxyyyyabklmopq"

$result = -join (($str1 + $str2).ToCharArray() | Sort-Object -Unique)
```

> **Screenshot 4**
>
> Show: One-liner solution
> Expected: Single line that produces result

Display the result:

```powershell
$result
```

Expected output:

```
abcdefklmopqwxy
```

> **Screenshot 5**
>
> Show: Final result
> Expected: "abcdefklmopqwxy"

Alternative solutions:

```powershell
# Using Select-Object -Unique
$result = -join (($str1 + $str2).ToCharArray() | Select-Object -Unique | Sort-Object)

# Using .NET HashSet for unique values
$chars = [System.Collections.Generic.HashSet[char]]::new(($str1 + $str2).ToCharArray())
$result = -join ($chars | Sort-Object)

# Using Get-Unique (requires sorted input first)
$result = -join (($str1 + $str2).ToCharArray() | Sort-Object | Get-Unique)
```

> **Screenshot 6**
>
> Show: Alternative solutions
> Expected: Different approaches with same result

As a function:

```powershell
function Get-SortedUniqueString {
    param(
        [string]$String1,
        [string]$String2
    )
    return -join (($String1 + $String2).ToCharArray() | Sort-Object -Unique)
}

# Usage
Get-SortedUniqueString -String1 "xyaabbbccccdefww" -String2 "xxxxyyyyabklmopq"
```

> **Screenshot 7**
>
> Show: Function implementation
> Expected: Reusable function with parameters


## TASK 2: Convert Dash/Underscore to CamelCase/PascalCase

Given strings:

```powershell
$inputString1 = "the-stealth-warrior"
$inputString2 = "The_Stealth_Warrior"
```

> **Screenshot 8**
>
> Show: Define input strings
> Expected: Both test strings created

Solution for camelCase (first word lowercase):

```powershell
function ConvertTo-CamelCase {
    param([string]$inputString)

    # Split by dash or underscore
    $words = $inputString -split '[-_]'

    # First word: lowercase
    $result = $words[0].ToLower()

    # Remaining words: capitalize first letter
    for ($i = 1; $i -lt $words.Length; $i++) {
        $word = $words[$i]
        $result += $word.Substring(0,1).ToUpper() + $word.Substring(1).ToLower()
    }

    return $result
}
```

> **Screenshot 9**
>
> Show: ConvertTo-CamelCase function
> Expected: Function definition

Test camelCase:

```powershell
ConvertTo-CamelCase "the-stealth-warrior"
```

Expected output:

```
theStealthWarrior
```

> **Screenshot 10**
>
> Show: CamelCase result
> Expected: "theStealthWarrior"

Solution for PascalCase (all words capitalized):

```powershell
function ConvertTo-PascalCase {
    param([string]$inputString)

    # Split by dash or underscore
    $words = $inputString -split '[-_]'

    # All words: capitalize first letter
    $result = ""
    foreach ($word in $words) {
        $result += $word.Substring(0,1).ToUpper() + $word.Substring(1).ToLower()
    }

    return $result
}
```

> **Screenshot 11**
>
> Show: ConvertTo-PascalCase function
> Expected: Function definition

Test PascalCase:

```powershell
ConvertTo-PascalCase "The_Stealth_Warrior"
```

Expected output:

```
TheStealthWarrior
```

> **Screenshot 12**
>
> Show: PascalCase result
> Expected: "TheStealthWarrior"

Combined function with switch:

```powershell
function ConvertTo-Case {
    param(
        [string]$inputString,
        [ValidateSet("Camel", "Pascal")]
        [string]$CaseType = "Camel"
    )

    $words = $inputString -split '[-_]'
    $result = ""

    for ($i = 0; $i -lt $words.Length; $i++) {
        $word = $words[$i]

        if ($i -eq 0 -and $CaseType -eq "Camel") {
            # First word lowercase for camelCase
            $result += $word.ToLower()
        } else {
            # Capitalize first letter
            $result += $word.Substring(0,1).ToUpper() + $word.Substring(1).ToLower()
        }
    }

    return $result
}

# Usage
ConvertTo-Case "the-stealth-warrior" -CaseType Camel   # theStealthWarrior
ConvertTo-Case "the-stealth-warrior" -CaseType Pascal  # TheStealthWarrior
```

> **Screenshot 13**
>
> Show: Combined function with CaseType parameter
> Expected: Both case types from single function

One-liner solutions:

```powershell
# CamelCase one-liner
$inputString = "the-stealth-warrior"
$words = $inputString -split '[-_]'
$result = $words[0].ToLower() + (($words | Select-Object -Skip 1 | ForEach-Object {
    $_.Substring(0,1).ToUpper() + $_.Substring(1).ToLower()
}) -join '')

# PascalCase one-liner
$inputString = "The_Stealth_Warrior"
$result = ($inputString -split '[-_]' | ForEach-Object {
    $_.Substring(0,1).ToUpper() + $_.Substring(1).ToLower()
}) -join ''
```

> **Screenshot 14**
>
> Show: One-liner solutions
> Expected: Compact code alternatives

Using .NET TextInfo (alternative approach):

```powershell
$textInfo = (Get-Culture).TextInfo

# Convert to Title Case then remove separators
$inputString = "the-stealth-warrior"
$titled = $textInfo.ToTitleCase($inputString.Replace("-", " ").Replace("_", " "))
$result = $titled.Replace(" ", "")

# For camelCase, lowercase the first character
$camelResult = $result.Substring(0,1).ToLower() + $result.Substring(1)
```

> **Screenshot 15**
>
> Show: .NET TextInfo approach
> Expected: Alternative using culture info

Additional test cases:

```powershell
# Test various inputs
ConvertTo-CamelCase "hello-world"           # helloWorld
ConvertTo-CamelCase "UPPER_CASE_TEST"       # upperCaseTest
ConvertTo-CamelCase "mixed-Case_Test"       # mixedCaseTest

ConvertTo-PascalCase "hello-world"          # HelloWorld
ConvertTo-PascalCase "UPPER_CASE_TEST"      # UpperCaseTest
ConvertTo-PascalCase "mixed-Case_Test"      # MixedCaseTest
```

> **Screenshot 16**
>
> Show: Multiple test cases
> Expected: Various inputs converted correctly


## COMPLETE SOLUTION SCRIPT

```powershell
# Task 1: Sorted Unique String
$str1 = "xyaabbbccccdefww"
$str2 = "xxxxyyyyabklmopq"
$sortedUnique = -join (($str1 + $str2).ToCharArray() | Sort-Object -Unique)
Write-Host "Sorted Unique: $sortedUnique"

# Task 2: Case Conversion Functions
function ConvertTo-CamelCase {
    param([string]$inputString)
    $words = $inputString -split '[-_]'
    $result = $words[0].ToLower()
    for ($i = 1; $i -lt $words.Length; $i++) {
        $word = $words[$i]
        $result += $word.Substring(0,1).ToUpper() + $word.Substring(1).ToLower()
    }
    return $result
}

function ConvertTo-PascalCase {
    param([string]$inputString)
    $words = $inputString -split '[-_]'
    $result = ""
    foreach ($word in $words) {
        $result += $word.Substring(0,1).ToUpper() + $word.Substring(1).ToLower()
    }
    return $result
}

# Test Task 2
Write-Host "CamelCase: $(ConvertTo-CamelCase 'the-stealth-warrior')"
Write-Host "PascalCase: $(ConvertTo-PascalCase 'The_Stealth_Warrior')"
```

> **Screenshot 17**
>
> Show: Complete solution script
> Expected: All tasks working together


## STRING METHODS REFERENCE

| Method              | Description                  | Example                    |
|---------------------|------------------------------|----------------------------|
| .ToCharArray()      | Convert to char array        | "abc".ToCharArray()        |
| .ToUpper()          | Convert to uppercase         | "abc".ToUpper()            |
| .ToLower()          | Convert to lowercase         | "ABC".ToLower()            |
| .Substring(i,n)     | Extract substring            | "hello".Substring(1,3)     |
| .Replace(a,b)       | Replace characters           | "cat".Replace("c","b")     |
| .Split(char)        | Split by character           | "a-b".Split("-")           |
| .Trim()             | Remove whitespace            | " hi ".Trim()              |
| .Length             | Get string length            | "hello".Length             |
| .IndexOf(s)         | Find position of substring   | "hello".IndexOf("l")       |


## POWERSHELL OPERATORS REFERENCE

| Operator            | Description                  | Example                    |
|---------------------|------------------------------|----------------------------|
| -split              | Split string by pattern      | "a-b" -split "-"           |
| -join               | Join array to string         | -join @("a","b")           |
| -replace            | Replace with regex           | "abc" -replace "b","x"     |
| -match              | Regex match                  | "abc" -match "b"           |
| +                   | Concatenate strings          | "a" + "b"                  |
| *                   | Repeat string                | "a" * 3                    |


## SORT-OBJECT OPTIONS

| Parameter           | Description                  | Example                    |
|---------------------|------------------------------|----------------------------|
| -Unique             | Remove duplicates            | Sort-Object -Unique        |
| -Descending         | Sort descending              | Sort-Object -Descending    |
| -Property           | Sort by property             | Sort-Object -Property Name |
| -CaseSensitive      | Case-sensitive sort          | Sort-Object -CaseSensitive |


## TROUBLESHOOTING

Problem: "You cannot call a method on a null-valued expression"
Solution: Check if string is empty before calling methods

```powershell
if ($inputString) { $inputString.ToCharArray() }
```

Problem: Substring throws index out of range
Solution: Check string length first

```powershell
if ($word.Length -gt 0) { $word.Substring(0,1) }
```

Problem: Split not working as expected
Solution: Use -split operator for regex, .Split() for simple chars

```powershell
"a-b_c" -split '[-_]'    # Regex pattern
"a-b".Split("-")         # Simple character
```

Problem: Sorting gives unexpected results
Solution: Ensure you're sorting the right type

```powershell
$str.ToCharArray() | Sort-Object    # Sort characters
$array | Sort-Object                 # Sort array items
```

Problem: First letter not capitalizing
Solution: Handle empty strings

```powershell
if ($word.Length -gt 0) {
    $word.Substring(0,1).ToUpper() + $word.Substring(1).ToLower()
}
```

Problem: Result has extra spaces
Solution: Use .Trim() and check join separator

```powershell
$result = ($words | ForEach-Object { ... }) -join ''  # Empty string join
```

> **Screenshot 18**
>
> Show: Summary of all completed tasks
> Expected: Both string manipulation tasks working


## SELF-REVIEW CHECKLIST

- [ ] Combined two strings into one
- [ ] Converted string to character array using ToCharArray()
- [ ] Used Sort-Object -Unique to sort and remove duplicates
- [ ] Joined array back to string using -join
- [ ] Result "abcdefklmopqwxy" achieved
- [ ] Split string by dash/underscore using -split '[-_]'
- [ ] Implemented CamelCase conversion (first word lowercase)
- [ ] Implemented PascalCase conversion (all words capitalized)
- [ ] "the-stealth-warrior" converts to "theStealthWarrior"
- [ ] "The_Stealth_Warrior" converts to "TheStealthWarrior"
- [ ] Understand Substring() method for case manipulation
- [ ] Know difference between -split operator and .Split() method
