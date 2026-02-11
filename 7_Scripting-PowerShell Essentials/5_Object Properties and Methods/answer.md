OBJECT PROPERTIES AND METHODS - ANSWER
======================================

This guide covers PowerShell classes, properties, methods,
and creating custom objects with functionality.


================================================================================
                     POWERSHELL CLASS BASICS
================================================================================

Class syntax:

    class ClassName {
        # Properties
        [type]$PropertyName

        # Constructor (optional)
        ClassName() {
            # initialization code
        }

        # Methods
        [returnType] MethodName([type]$param) {
            # method body
            return $value
        }
    }

Creating an instance:

    $object = [ClassName]::new()
    # or
    $object = New-Object ClassName

================================================================================


TASK 1: PowerShell Class with Pow() Method
-------------------------------------------

Create the class with Pow method:

    class MathOperations {
        # Method to calculate power (base^exponent)
        [int] Pow([int]$base, [int]$exponent) {
            return [Math]::Pow($base, $exponent)
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: MathOperations class definition                   |
|  Expected: Class with Pow method                         |
+----------------------------------------------------------+

Create an instance and use it:

    $MyCustomObject = [MathOperations]::new()

Test Example 1:

    $MyCustomObject.Pow(2, 3)

Expected output:

    8

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Pow(2, 3) execution                               |
|  Expected: Returns 8                                     |
+----------------------------------------------------------+

Test Example 2:

    $MyCustomObject.Pow(7, 2)

Expected output:

    49

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Pow(7, 2) execution                               |
|  Expected: Returns 49                                    |
+----------------------------------------------------------+

Alternative implementation (without [Math] class):

    class MathOperations {
        [int] Pow([int]$base, [int]$exponent) {
            $result = 1
            for ($i = 0; $i -lt $exponent; $i++) {
                $result *= $base
            }
            return $result
        }
    }

Or using the ** operator (PowerShell 7+):

    class MathOperations {
        [double] Pow([double]$base, [double]$exponent) {
            return $base ** $exponent
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Alternative Pow implementations                   |
|  Expected: Different approaches to calculate power       |
+----------------------------------------------------------+


TASK 2: Class with SetString() and PrintString() Methods
--------------------------------------------------------

Create the class with string manipulation:

    class StringHandler {
        # Property to store the string
        [string]$StoredString

        # Method to set/store the string
        [void] SetString([string]$inputString) {
            $this.StoredString = $inputString
        }

        # Method to print string in uppercase
        [void] PrintString() {
            Write-Host $this.StoredString.ToUpper()
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: StringHandler class definition                    |
|  Expected: Class with SetString and PrintString          |
+----------------------------------------------------------+

Create an instance:

    $MyCustomObject = [StringHandler]::new()

Test Example 1 - Set the string:

    $MyCustomObject.SetString("You shall not pass!")

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: SetString() execution                             |
|  Expected: No output (void method)                       |
+----------------------------------------------------------+

Test Example 2 - Print the string:

    $MyCustomObject.PrintString()

Expected output:

    YOU SHALL NOT PASS!

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: PrintString() execution                           |
|  Expected: YOU SHALL NOT PASS!                           |
+----------------------------------------------------------+

Complete test session:

    PS> $MyCustomObject = [StringHandler]::new()
    PS> $MyCustomObject.SetString("You shall not pass!")
    PS> $MyCustomObject.PrintString()
    YOU SHALL NOT PASS!

Alternative - With return instead of Write-Host:

    class StringHandler {
        [string]$StoredString

        [void] SetString([string]$inputString) {
            $this.StoredString = $inputString
        }

        [string] PrintString() {
            return $this.StoredString.ToUpper()
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Complete SetString/PrintString workflow           |
|  Expected: Full test session visible                     |
+----------------------------------------------------------+


TASK 3: Class to Reverse a String
----------------------------------

Create the class with Reverse method:

    class StringReverser {
        [string] Reverse([string]$inputString) {
            $charArray = $inputString.ToCharArray()
            [Array]::Reverse($charArray)
            return -join $charArray
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: StringReverser class definition                   |
|  Expected: Class with Reverse method                     |
+----------------------------------------------------------+

Create an instance:

    $MyCustomObject = [StringReverser]::new()

Test the Reverse method:

    $MyCustomObject.Reverse("Nostromo")

Expected output:

    omortsoN

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Reverse("Nostromo") execution                     |
|  Expected: Returns omortsoN                              |
+----------------------------------------------------------+

Additional test cases:

    $MyCustomObject.Reverse("PowerShell")
    # Returns: llehSrewoP

    $MyCustomObject.Reverse("Hello World")
    # Returns: dlroW olleH

    $MyCustomObject.Reverse("12345")
    # Returns: 54321

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Multiple Reverse test cases                       |
|  Expected: Various reversed strings                      |
+----------------------------------------------------------+

Alternative implementations:

    # Using string indexing
    class StringReverser {
        [string] Reverse([string]$inputString) {
            $reversed = ""
            for ($i = $inputString.Length - 1; $i -ge 0; $i--) {
                $reversed += $inputString[$i]
            }
            return $reversed
        }
    }

    # Using .NET StringBuilder
    class StringReverser {
        [string] Reverse([string]$inputString) {
            $sb = [System.Text.StringBuilder]::new()
            for ($i = $inputString.Length - 1; $i -ge 0; $i--) {
                [void]$sb.Append($inputString[$i])
            }
            return $sb.ToString()
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Alternative Reverse implementations               |
|  Expected: Different approaches shown                    |
+----------------------------------------------------------+


COMPLETE SOLUTION - ALL THREE CLASSES
-------------------------------------

You can combine all functionality in one class:

    class MyCustomClass {
        # Property for string storage
        [string]$StoredString

        # Task 1: Power method
        [double] Pow([double]$base, [double]$exponent) {
            return [Math]::Pow($base, $exponent)
        }

        # Task 2: Set string method
        [void] SetString([string]$inputString) {
            $this.StoredString = $inputString
        }

        # Task 2: Print uppercase method
        [void] PrintString() {
            Write-Host $this.StoredString.ToUpper()
        }

        # Task 3: Reverse string method
        [string] Reverse([string]$inputString) {
            $charArray = $inputString.ToCharArray()
            [Array]::Reverse($charArray)
            return -join $charArray
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Combined class with all methods                   |
|  Expected: Complete MyCustomClass definition             |
+----------------------------------------------------------+

Usage:

    $MyCustomObject = [MyCustomClass]::new()

    # Task 1
    $MyCustomObject.Pow(2, 3)        # 8
    $MyCustomObject.Pow(7, 2)        # 49

    # Task 2
    $MyCustomObject.SetString("You shall not pass!")
    $MyCustomObject.PrintString()    # YOU SHALL NOT PASS!

    # Task 3
    $MyCustomObject.Reverse("Nostromo")  # omortsoN

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: All methods tested                                |
|  Expected: All three tasks working                       |
+----------------------------------------------------------+


================================================================================
                     CLASS SYNTAX REFERENCE
================================================================================

| Element          | Syntax                              | Example            |
|------------------|-------------------------------------|--------------------|
| Class definition | class Name { }                      | class MyClass { }  |
| Property         | [type]$Name                         | [string]$Value     |
| Method           | [return] Name([params]) { }         | [int] Add() { }    |
| Constructor      | ClassName() { }                     | MyClass() { }      |
| This reference   | $this.Property                      | $this.Value        |
| Static method    | static [type] Name() { }            | static [int] Get() |
| Create instance  | [ClassName]::new()                  | [MyClass]::new()   |

================================================================================


================================================================================
                     COMMON STRING METHODS
================================================================================

| Method           | Description                    | Example                  |
|------------------|--------------------------------|--------------------------|
| .ToUpper()       | Convert to uppercase           | "abc".ToUpper() = "ABC"  |
| .ToLower()       | Convert to lowercase           | "ABC".ToLower() = "abc"  |
| .Length          | Get string length              | "hello".Length = 5       |
| .Substring(i,n)  | Get substring                  | "hello".Substring(1,3)   |
| .Replace(a,b)    | Replace characters             | "cat".Replace("c","b")   |
| .Split(char)     | Split into array               | "a,b".Split(",")         |
| .Trim()          | Remove whitespace              | " hi ".Trim() = "hi"     |
| .Contains(s)     | Check if contains              | "abc".Contains("b")      |
| .StartsWith(s)   | Check if starts with           | "abc".StartsWith("a")    |
| .ToCharArray()   | Convert to char array          | "abc".ToCharArray()      |

================================================================================


================================================================================
                     MATH CLASS METHODS
================================================================================

| Method           | Description                    | Example                  |
|------------------|--------------------------------|--------------------------|
| [Math]::Pow(x,y) | Power (x^y)                    | [Math]::Pow(2,3) = 8     |
| [Math]::Sqrt(x)  | Square root                    | [Math]::Sqrt(16) = 4     |
| [Math]::Abs(x)   | Absolute value                 | [Math]::Abs(-5) = 5      |
| [Math]::Round(x) | Round number                   | [Math]::Round(3.7) = 4   |
| [Math]::Floor(x) | Round down                     | [Math]::Floor(3.9) = 3   |
| [Math]::Ceiling(x)| Round up                      | [Math]::Ceiling(3.1) = 4 |
| [Math]::Max(x,y) | Maximum value                  | [Math]::Max(5,3) = 5     |
| [Math]::Min(x,y) | Minimum value                  | [Math]::Min(5,3) = 3     |

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Unable to find type [ClassName]"
Solution: Class must be defined before use in same session
    # Define class first, then create instance

Problem: "Method not found"
Solution: Check method name spelling and parameters
    $obj.MethodName()    # Correct case matters

Problem: "Cannot convert argument"
Solution: Check parameter types match
    [int] Pow([int]$x, [int]$y)    # Expects integers

Problem: "$this is not recognized"
Solution: $this only works inside class methods
    $this.Property    # Must be inside method

Problem: "Property is read-only"
Solution: Check property definition
    [string]$Prop    # Writable by default

Problem: Class changes not taking effect
Solution: Redefine class after changes (or restart PowerShell)
    # PowerShell caches class definitions

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Summary of all completed tasks                    |
|  Expected: All three methods working correctly           |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Created class with Pow() method
[ ] Pow(2, 3) returns 8
[ ] Pow(7, 2) returns 49
[ ] Created class with SetString() and PrintString() methods
[ ] SetString() stores the input string
[ ] PrintString() outputs uppercase version
[ ] "You shall not pass!" becomes "YOU SHALL NOT PASS!"
[ ] Created class with Reverse() method
[ ] Reverse("Nostromo") returns "omortsoN"
[ ] Understand $this keyword for accessing properties
[ ] Understand [void] return type for methods without return
[ ] Know how to create class instances with ::new()

================================================================================
