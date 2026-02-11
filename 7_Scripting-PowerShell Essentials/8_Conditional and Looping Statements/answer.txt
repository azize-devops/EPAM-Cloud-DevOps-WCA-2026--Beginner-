CONDITIONAL AND LOOPING STATEMENTS - ANSWER
===========================================

This guide covers PowerShell conditional statements (if/else, switch)
and looping constructs (for, while, foreach) with practical examples.


================================================================================
                     CONDITIONALS AND LOOPS OVERVIEW
================================================================================

Conditional statements:
    if (condition) { }
    elseif (condition) { }
    else { }
    switch (value) { }

Loop statements:
    for ($i = 0; $i -lt 10; $i++) { }
    foreach ($item in $collection) { }
    while (condition) { }
    do { } while (condition)
    do { } until (condition)

================================================================================


TASK 1: Temperature Conversion (Celsius / Fahrenheit)
------------------------------------------------------

Formulas:
    Celsius to Fahrenheit: F = (C * 9/5) + 32
    Fahrenheit to Celsius: C = (F - 32) * 5/9

Create script1.ps1:

    param([string]$temperature)

    # Extract number and unit
    if ($temperature -match '^(\d+)([CF])$') {
        $value = [int]$Matches[1]
        $unit = $Matches[2]

        if ($unit -eq 'C') {
            # Celsius to Fahrenheit
            $result = ($value * 9/5) + 32
            Write-Host "${value}C is $([int]$result) in Fahrenheit"
        }
        elseif ($unit -eq 'F') {
            # Fahrenheit to Celsius
            $result = ($value - 32) * 5/9
            Write-Host "${value}F is $([int]$result) in Celsius"
        }
    }
    else {
        Write-Host "Invalid format. Use: 60C or 45F"
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: script1.ps1 content                               |
|  Expected: Temperature conversion script                 |
+----------------------------------------------------------+

Test Example 1 - Celsius to Fahrenheit:

    & .\script1.ps1 60C

Expected output:

    60C is 140 in Fahrenheit

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: 60C conversion                                    |
|  Expected: 60C is 140 in Fahrenheit                      |
+----------------------------------------------------------+

Test Example 2 - Fahrenheit to Celsius:

    & .\script1.ps1 45F

Expected output:

    45F is 7 in Celsius

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: 45F conversion                                    |
|  Expected: 45F is 7 in Celsius                           |
+----------------------------------------------------------+

Alternative using switch:

    param([string]$temperature)

    if ($temperature -match '^(\d+)([CF])$') {
        $value = [int]$Matches[1]
        $unit = $Matches[2]

        switch ($unit) {
            'C' {
                $result = [int](($value * 9/5) + 32)
                "${value}C is $result in Fahrenheit"
            }
            'F' {
                $result = [int](($value - 32) * 5/9)
                "${value}F is $result in Celsius"
            }
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Switch statement alternative                      |
|  Expected: Same functionality with switch                |
+----------------------------------------------------------+

Additional tests:

    & .\script1.ps1 100C    # 100C is 212 in Fahrenheit (boiling point)
    & .\script1.ps1 32F     # 32F is 0 in Celsius (freezing point)
    & .\script1.ps1 0C      # 0C is 32 in Fahrenheit


TASK 2: Fibonacci Series Between 0 to N
----------------------------------------

Fibonacci: Each number is sum of two preceding ones
    1, 1, 2, 3, 5, 8, 13, 21, 34, 55, ...

Create script2.ps1:

    param([int]$n)

    $a = 0
    $b = 1
    $result = @()

    while ($b -le $n) {
        $result += $b
        $temp = $a + $b
        $a = $b
        $b = $temp
    }

    $result -join ' '

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: script2.ps1 content                               |
|  Expected: Fibonacci series script                       |
+----------------------------------------------------------+

Test:

    & .\script2.ps1 50

Expected output:

    1 1 2 3 5 8 13 21 34

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Fibonacci up to 50                                |
|  Expected: 1 1 2 3 5 8 13 21 34                          |
+----------------------------------------------------------+

Alternative using for loop:

    param([int]$n)

    $fib = @(1, 1)
    $result = @(1, 1)

    for ($i = 2; $fib[$i-1] + $fib[$i-2] -le $n; $i++) {
        $next = $fib[$i-1] + $fib[$i-2]
        $fib += $next
        $result += $next
    }

    $result -join ' '

Alternative using do-while:

    param([int]$n)

    $a = 0
    $b = 1
    $output = @()

    do {
        $output += $b
        $a, $b = $b, ($a + $b)
    } while ($b -le $n)

    $output -join ' '

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Alternative Fibonacci implementations             |
|  Expected: for loop and do-while versions                |
+----------------------------------------------------------+

Additional tests:

    & .\script2.ps1 100     # 1 1 2 3 5 8 13 21 34 55 89
    & .\script2.ps1 10      # 1 1 2 3 5 8
    & .\script2.ps1 1000    # 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987


TASK 3: Dog's Age to Human Years
---------------------------------

Formula:
    Year 1: 10.5 human years
    Year 2: 10.5 human years (total: 21)
    Year 3+: 4 human years each

Create script3.ps1:

    param([int]$dogYears)

    $result = @()

    for ($year = 1; $year -le $dogYears; $year++) {
        if ($year -le 2) {
            # First two years: 10.5 human years each
            $humanAge = $year * 10.5
        }
        else {
            # After that: 21 + (years-2) * 4
            $humanAge = 21 + ($year - 2) * 4
        }
        $result += $humanAge
    }

    $result -join ' '

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: script3.ps1 content                               |
|  Expected: Dog age conversion script                     |
+----------------------------------------------------------+

Test:

    & .\script3.ps1 10

Expected output:

    10.5 21 25 29 33 37 41 45 49 53

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: 10 dog years conversion                           |
|  Expected: 10.5 21 25 29 33 37 41 45 49 53               |
+----------------------------------------------------------+

Step-by-step breakdown:
    Year 1:  10.5 (1 * 10.5)
    Year 2:  21   (2 * 10.5)
    Year 3:  25   (21 + 1*4)
    Year 4:  29   (21 + 2*4)
    Year 5:  33   (21 + 3*4)
    Year 6:  37   (21 + 4*4)
    Year 7:  41   (21 + 5*4)
    Year 8:  45   (21 + 6*4)
    Year 9:  49   (21 + 7*4)
    Year 10: 53   (21 + 8*4)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Formula breakdown                                 |
|  Expected: Step-by-step calculation visible              |
+----------------------------------------------------------+

Alternative using foreach with range:

    param([int]$dogYears)

    $result = @()
    $humanAge = 0

    foreach ($year in 1..$dogYears) {
        if ($year -le 2) {
            $humanAge += 10.5
        }
        else {
            $humanAge += 4
        }
        $result += $humanAge
    }

    $result -join ' '

Alternative using ternary-like expression (PowerShell 7+):

    param([int]$dogYears)

    (1..$dogYears | ForEach-Object {
        if ($_ -le 2) { $_ * 10.5 } else { 21 + ($_ - 2) * 4 }
    }) -join ' '

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Alternative implementations                       |
|  Expected: foreach and pipeline versions                 |
+----------------------------------------------------------+

Additional tests:

    & .\script3.ps1 1       # 10.5
    & .\script3.ps1 2       # 10.5 21
    & .\script3.ps1 5       # 10.5 21 25 29 33


TASK 4: Multiplication Table (1 to 10)
---------------------------------------

Create script4.ps1:

    param([int]$number)

    for ($i = 1; $i -le 10; $i++) {
        $result = $number * $i
        Write-Host "$number x $i = $result"
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: script4.ps1 content                               |
|  Expected: Multiplication table script                   |
+----------------------------------------------------------+

Test:

    & .\script4.ps1 6

Expected output:

    6 x 1 = 6
    6 x 2 = 12
    6 x 3 = 18
    6 x 4 = 24
    6 x 5 = 30
    6 x 6 = 36
    6 x 7 = 42
    6 x 8 = 48
    6 x 9 = 54
    6 x 10 = 60

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Multiplication table for 6                        |
|  Expected: 6 x 1 through 6 x 10                          |
+----------------------------------------------------------+

Alternative using foreach with range:

    param([int]$number)

    foreach ($i in 1..10) {
        "$number x $i = $($number * $i)"
    }

Alternative using ForEach-Object:

    param([int]$number)

    1..10 | ForEach-Object {
        "$number x $_ = $($number * $_)"
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Alternative multiplication implementations        |
|  Expected: foreach and pipeline versions                 |
+----------------------------------------------------------+

Formatted output with alignment:

    param([int]$number)

    for ($i = 1; $i -le 10; $i++) {
        $result = $number * $i
        "{0,3} x {1,2} = {2,4}" -f $number, $i, $result
    }

Output:
      6 x  1 =    6
      6 x  2 =   12
      6 x  3 =   18
      ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Formatted output version                          |
|  Expected: Aligned columns                               |
+----------------------------------------------------------+

Additional tests:

    & .\script4.ps1 7       # 7 x 1 = 7 ... 7 x 10 = 70
    & .\script4.ps1 12      # 12 x 1 = 12 ... 12 x 10 = 120
    & .\script4.ps1 1       # 1 x 1 = 1 ... 1 x 10 = 10


COMPLETE SCRIPT FILES
---------------------

script1.ps1 (Temperature Conversion):

    param([string]$temperature)

    if ($temperature -match '^(\d+)([CF])$') {
        $value = [int]$Matches[1]
        $unit = $Matches[2]

        if ($unit -eq 'C') {
            $result = ($value * 9/5) + 32
            Write-Host "${value}C is $([int]$result) in Fahrenheit"
        }
        elseif ($unit -eq 'F') {
            $result = ($value - 32) * 5/9
            Write-Host "${value}F is $([int]$result) in Celsius"
        }
    }

script2.ps1 (Fibonacci Series):

    param([int]$n)

    $a = 0
    $b = 1
    $result = @()

    while ($b -le $n) {
        $result += $b
        $temp = $a + $b
        $a = $b
        $b = $temp
    }

    $result -join ' '

script3.ps1 (Dog Years):

    param([int]$dogYears)

    $result = @()

    for ($year = 1; $year -le $dogYears; $year++) {
        if ($year -le 2) {
            $humanAge = $year * 10.5
        }
        else {
            $humanAge = 21 + ($year - 2) * 4
        }
        $result += $humanAge
    }

    $result -join ' '

script4.ps1 (Multiplication Table):

    param([int]$number)

    for ($i = 1; $i -le 10; $i++) {
        $result = $number * $i
        Write-Host "$number x $i = $result"
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: All four script files                             |
|  Expected: script1.ps1 through script4.ps1               |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: All scripts tested                                |
|  Expected: Complete test run of all scripts              |
+----------------------------------------------------------+


================================================================================
                     CONDITIONAL STATEMENTS REFERENCE
================================================================================

| Statement    | Syntax                              | Description            |
|--------------|-------------------------------------|------------------------|
| if           | if (condition) { }                  | Basic condition        |
| elseif       | elseif (condition) { }              | Additional condition   |
| else         | else { }                            | Default case           |
| switch       | switch (value) { 'a' { } }          | Multiple conditions    |

Examples:

    # If-ElseIf-Else
    if ($x -gt 10) {
        "Greater than 10"
    } elseif ($x -gt 5) {
        "Greater than 5"
    } else {
        "5 or less"
    }

    # Switch
    switch ($color) {
        'red'   { "Stop" }
        'yellow' { "Caution" }
        'green' { "Go" }
        default { "Unknown" }
    }

================================================================================


================================================================================
                     LOOP STATEMENTS REFERENCE
================================================================================

| Loop         | Syntax                              | Use Case               |
|--------------|-------------------------------------|------------------------|
| for          | for ($i=0; $i -lt N; $i++) { }      | Known iteration count  |
| foreach      | foreach ($x in $array) { }          | Iterate collection     |
| while        | while (condition) { }               | Unknown iterations     |
| do-while     | do { } while (condition)            | Execute at least once  |
| do-until     | do { } until (condition)            | Until condition true   |

Examples:

    # For loop
    for ($i = 1; $i -le 10; $i++) {
        Write-Host $i
    }

    # Foreach loop
    foreach ($item in @(1,2,3,4,5)) {
        Write-Host $item
    }

    # While loop
    $count = 0
    while ($count -lt 5) {
        Write-Host $count
        $count++
    }

    # Range operator
    foreach ($n in 1..10) {
        Write-Host $n
    }

================================================================================


================================================================================
                     COMPARISON OPERATORS
================================================================================

| Operator     | Description                    | Example                    |
|--------------|--------------------------------|----------------------------|
| -eq          | Equal                          | $a -eq $b                  |
| -ne          | Not equal                      | $a -ne $b                  |
| -gt          | Greater than                   | $a -gt 10                  |
| -ge          | Greater or equal               | $a -ge 10                  |
| -lt          | Less than                      | $a -lt 10                  |
| -le          | Less or equal                  | $a -le 10                  |
| -like        | Wildcard match                 | $s -like "*.txt"           |
| -match       | Regex match                    | $s -match '\d+'            |
| -contains    | Collection contains            | $arr -contains 5           |
| -in          | Value in collection            | 5 -in $arr                 |

================================================================================


================================================================================
                     LOOP CONTROL STATEMENTS
================================================================================

| Statement    | Description                    | Example                    |
|--------------|--------------------------------|----------------------------|
| break        | Exit loop immediately          | if ($x -eq 5) { break }    |
| continue     | Skip to next iteration         | if ($x -eq 5) { continue } |
| return       | Exit function/script           | return $result             |

Example:

    for ($i = 1; $i -le 10; $i++) {
        if ($i -eq 3) { continue }  # Skip 3
        if ($i -eq 8) { break }     # Stop at 8
        Write-Host $i
    }
    # Output: 1, 2, 4, 5, 6, 7

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: Script not accepting parameters
Solution: Add param() block at the very top
    param([int]$number)
    # Rest of script

Problem: "Cannot convert value" error
Solution: Ensure correct type casting
    [int]$value = $Matches[1]
    [double]$result = $x / $y

Problem: Infinite loop
Solution: Ensure loop condition will eventually be false
    while ($b -le $n) {    # Check condition changes
        $b = $a + $b       # Update variable
    }

Problem: Off-by-one errors in loops
Solution: Check loop boundaries
    for ($i = 1; $i -le 10; $i++)   # 1 to 10 inclusive
    for ($i = 0; $i -lt 10; $i++)   # 0 to 9 inclusive

Problem: Array index out of bounds
Solution: Check array length before accessing
    if ($i -lt $array.Length) { $array[$i] }

Problem: Variable scope in loops
Solution: Variables inside loops are accessible outside
    $result = @()
    foreach ($x in $items) {
        $result += $x
    }
    $result  # Available here

Problem: Floating point precision
Solution: Use [Math]::Round() when needed
    $humanAge = [Math]::Round($year * 10.5, 1)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: Summary of all conditional and loop tasks         |
|  Expected: All four scripts working correctly            |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Task 1: Script detects C or F suffix in input
[ ] Task 1: Celsius to Fahrenheit: F = (C * 9/5) + 32
[ ] Task 1: Fahrenheit to Celsius: C = (F - 32) * 5/9
[ ] Task 1: 60C returns 140F, 45F returns 7C
[ ] Task 2: Fibonacci series starts with 1 1
[ ] Task 2: Each number is sum of previous two
[ ] Task 2: Series stops when value exceeds N
[ ] Task 2: 50 returns "1 1 2 3 5 8 13 21 34"
[ ] Task 3: First two years = 10.5 human years each
[ ] Task 3: Years 3+ = 4 human years each
[ ] Task 3: Cumulative ages displayed for each year
[ ] Task 3: 10 returns "10.5 21 25 29 33 37 41 45 49 53"
[ ] Task 4: Multiplication from 1 to 10
[ ] Task 4: Format is "N x M = Result"
[ ] Task 4: 6 shows 6x1=6 through 6x10=60
[ ] Understand if/elseif/else structure
[ ] Understand for, foreach, and while loops
[ ] Know how to use range operator (..)
[ ] Know how to join array elements with -join

================================================================================
