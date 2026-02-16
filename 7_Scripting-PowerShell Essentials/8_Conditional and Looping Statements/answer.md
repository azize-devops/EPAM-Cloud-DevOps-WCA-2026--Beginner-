# CONDITIONAL AND LOOPING STATEMENTS - ANSWER

This guide covers PowerShell control flow: if/else, switch, for, foreach,
while, do-while, and do-until loops.


## CONTROL FLOW OVERVIEW

Conditional statements:

```powershell
if (condition) { }
elseif (condition) { }
else { }

switch (value) {
    pattern { }
    default { }
}
```

Loop statements:

```powershell
for (init; condition; increment) { }
foreach ($item in $collection) { }
while (condition) { }
do { } while (condition)
do { } until (condition)
```


## TASK 1: FizzBuzz Implementation

Goal: Print numbers 1-100. For multiples of 3 print "Fizz",
for multiples of 5 print "Buzz", for both print "FizzBuzz".

Solution using if/elseif/else:

```powershell
for ($i = 1; $i -le 100; $i++) {
    if ($i % 3 -eq 0 -and $i % 5 -eq 0) {
        Write-Host "FizzBuzz"
    }
    elseif ($i % 3 -eq 0) {
        Write-Host "Fizz"
    }
    elseif ($i % 5 -eq 0) {
        Write-Host "Buzz"
    }
    else {
        Write-Host $i
    }
}
```

> **Screenshot 1**
>
> Show: FizzBuzz solution
> Expected: Code with if/elseif/else

Expected output (partial):

```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
16
...
```

> **Screenshot 2**
>
> Show: FizzBuzz output
> Expected: Numbers with Fizz, Buzz, FizzBuzz

Alternative using switch:

```powershell
1..100 | ForEach-Object {
    switch ($_) {
        { $_ % 15 -eq 0 } { "FizzBuzz"; break }
        { $_ % 3 -eq 0 }  { "Fizz"; break }
        { $_ % 5 -eq 0 }  { "Buzz"; break }
        default { $_ }
    }
}
```

> **Screenshot 3**
>
> Show: Switch-based FizzBuzz
> Expected: Alternative solution

Alternative - string concatenation approach:

```powershell
for ($i = 1; $i -le 100; $i++) {
    $output = ""

    if ($i % 3 -eq 0) { $output += "Fizz" }
    if ($i % 5 -eq 0) { $output += "Buzz" }

    if ($output -eq "") {
        Write-Host $i
    }
    else {
        Write-Host $output
    }
}
```

> **Screenshot 4**
>
> Show: String concatenation approach
> Expected: Cleaner logic


## TASK 2: Sum of Numbers in Range

Goal: Calculate sum of all numbers from 1 to N.

Solution using for loop:

```powershell
function Get-Sum {
    param([int]$N)

    $sum = 0
    for ($i = 1; $i -le $N; $i++) {
        $sum += $i
    }
    return $sum
}

# Test
Get-Sum -N 10
# Output: 55 (1+2+3+4+5+6+7+8+9+10)

Get-Sum -N 100
# Output: 5050
```

> **Screenshot 5**
>
> Show: Sum function with for loop
> Expected: Function definition

Solution using while loop:

```powershell
function Get-Sum {
    param([int]$N)

    $sum = 0
    $i = 1
    while ($i -le $N) {
        $sum += $i
        $i++
    }
    return $sum
}
```

> **Screenshot 6**
>
> Show: Sum function with while loop
> Expected: Alternative implementation

Solution using pipeline:

```powershell
function Get-Sum {
    param([int]$N)

    return (1..$N | Measure-Object -Sum).Sum
}
```

> **Screenshot 7**
>
> Show: Pipeline-based sum
> Expected: One-liner solution

Mathematical formula (most efficient):

```powershell
function Get-Sum {
    param([int]$N)

    return ($N * ($N + 1)) / 2
}
```

> **Screenshot 8**
>
> Show: Mathematical formula
> Expected: O(1) solution


## TASK 3: Iterate Through Array

Goal: Loop through array and display each element.

Solution using foreach:

```powershell
$fruits = @("Apple", "Banana", "Cherry", "Date", "Elderberry")

foreach ($fruit in $fruits) {
    Write-Host "Fruit: $fruit"
}
```

Expected output:

```
Fruit: Apple
Fruit: Banana
Fruit: Cherry
Fruit: Date
Fruit: Elderberry
```

> **Screenshot 9**
>
> Show: foreach loop
> Expected: All fruits displayed

Solution using for loop with index:

```powershell
$fruits = @("Apple", "Banana", "Cherry", "Date", "Elderberry")

for ($i = 0; $i -lt $fruits.Count; $i++) {
    Write-Host "[$i] $($fruits[$i])"
}
```

Expected output:

```
[0] Apple
[1] Banana
[2] Cherry
[3] Date
[4] Elderberry
```

> **Screenshot 10**
>
> Show: for loop with index
> Expected: Indexed output

Solution using ForEach-Object pipeline:

```powershell
$fruits | ForEach-Object {
    Write-Host "Fruit: $_"
}
```

> **Screenshot 11**
>
> Show: Pipeline ForEach-Object
> Expected: Same output, different syntax


## TASK 4: Process Until Condition Met

Goal: Loop until user enters "quit" or specific condition.

Solution using do-until:

```powershell
do {
    $input = Read-Host "Enter a command (type 'quit' to exit)"

    if ($input -ne "quit") {
        Write-Host "You entered: $input"
    }
} until ($input -eq "quit")

Write-Host "Goodbye!"
```

> **Screenshot 12**
>
> Show: do-until loop
> Expected: Interactive input loop

Solution using do-while:

```powershell
do {
    $number = Read-Host "Enter a positive number"
    $number = [int]$number

    if ($number -le 0) {
        Write-Host "Please enter a positive number!"
    }
} while ($number -le 0)

Write-Host "You entered: $number"
```

> **Screenshot 13**
>
> Show: do-while validation
> Expected: Input validation loop

Break and continue:

```powershell
# Break - exit loop entirely
foreach ($i in 1..10) {
    if ($i -eq 5) { break }
    Write-Host $i
}
# Output: 1, 2, 3, 4

# Continue - skip to next iteration
foreach ($i in 1..10) {
    if ($i % 2 -eq 0) { continue }
    Write-Host $i
}
# Output: 1, 3, 5, 7, 9
```

> **Screenshot 14**
>
> Show: Break and continue
> Expected: Control flow demonstrated


## COMPLETE SOLUTION SCRIPT

```powershell
# FizzBuzz (Task 1)
Write-Host "=== FizzBuzz ===" -ForegroundColor Cyan
for ($i = 1; $i -le 20; $i++) {
    if ($i % 15 -eq 0) { Write-Host "FizzBuzz" }
    elseif ($i % 3 -eq 0) { Write-Host "Fizz" }
    elseif ($i % 5 -eq 0) { Write-Host "Buzz" }
    else { Write-Host $i }
}

# Sum of Numbers (Task 2)
Write-Host "`n=== Sum ===" -ForegroundColor Cyan
$n = 10
$sum = 0
for ($i = 1; $i -le $n; $i++) { $sum += $i }
Write-Host "Sum of 1 to $n = $sum"

# Array Iteration (Task 3)
Write-Host "`n=== Array ===" -ForegroundColor Cyan
$items = @("One", "Two", "Three")
foreach ($item in $items) {
    Write-Host "Item: $item"
}

# Loop with Condition (Task 4)
Write-Host "`n=== While Loop ===" -ForegroundColor Cyan
$count = 0
while ($count -lt 5) {
    Write-Host "Count: $count"
    $count++
}
```

> **Screenshot 15**
>
> Show: Complete solution script
> Expected: All tasks demonstrated


## LOOP COMPARISON

| Loop Type      | Best For                            | Example              |
|----------------|-------------------------------------|----------------------|
| for            | Known number of iterations          | for ($i=0; $i-lt 10) |
| foreach        | Iterating through collections       | foreach ($x in $arr) |
| ForEach-Object | Pipeline processing                 | $arr | ForEach {...} |
| while          | Loop while condition is true        | while ($x -lt 10)    |
| do-while       | Execute at least once, check after  | do {} while ($x)     |
| do-until       | Execute until condition becomes true| do {} until ($x)     |


## SWITCH STATEMENT

Basic switch:

```powershell
switch ($value) {
    1 { "One" }
    2 { "Two" }
    3 { "Three" }
    default { "Unknown" }
}
```

Switch with conditions:

```powershell
$number = 15
switch ($number) {
    { $_ -lt 0 } { "Negative" }
    { $_ -eq 0 } { "Zero" }
    { $_ -gt 0 -and $_ -le 10 } { "1-10" }
    { $_ -gt 10 } { "Greater than 10" }
}
```

Switch with regex:

```powershell
$email = "user@example.com"
switch -Regex ($email) {
    '@gmail\.com$' { "Gmail address" }
    '@outlook\.com$' { "Outlook address" }
    '@.+\.edu$' { "Educational address" }
    default { "Other email" }
}
```


## TROUBLESHOOTING

Problem: Infinite loop
Solution: Ensure condition eventually becomes false

```powershell
$i = 0
while ($i -lt 10) {
    $i++  # Don't forget to increment!
}
```

Problem: Off-by-one errors
Solution: Check -lt vs -le, and starting index

```powershell
for ($i = 0; $i -lt $arr.Count; $i++)  # Correct: 0 to Count-1
for ($i = 1; $i -le $arr.Count; $i++)  # Wrong for 0-indexed
```

Problem: foreach modifying collection
Solution: Don't modify collection during iteration

```powershell
# Wrong:
foreach ($item in $list) { $list.Remove($item) }

# Right:
$toRemove = $list | Where-Object { $_.Value -eq 0 }
$toRemove | ForEach-Object { $list.Remove($_) }
```

Problem: Switch falling through
Solution: Use break if needed (PowerShell doesn't fall through by default)

```powershell
switch ($x) {
    1 { "One"; break }
    2 { "Two"; break }
}
```

Problem: Loop variable scope
Solution: Variables declared in loop are available outside

```powershell
foreach ($i in 1..5) { }
$i  # Still accessible, value is 5
```

> **Screenshot 16**
>
> Show: Summary of loop tasks
> Expected: All control flow working


## SELF-REVIEW CHECKLIST

- [ ] FizzBuzz prints correct output for 1-100
- [ ] Understands modulo operator (%) for divisibility
- [ ] Sum function calculates correct total
- [ ] Knows multiple ways to sum: for, while, pipeline
- [ ] Can iterate through array with foreach
- [ ] Can iterate with index using for loop
- [ ] Understands do-while vs do-until difference
- [ ] Knows when to use break vs continue
- [ ] Can use switch statement with conditions
- [ ] Understands ForEach-Object vs foreach keyword
- [ ] Knows common loop pitfalls (infinite loops, off-by-one)
