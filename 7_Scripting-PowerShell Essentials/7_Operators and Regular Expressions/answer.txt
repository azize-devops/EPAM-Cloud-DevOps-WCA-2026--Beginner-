OPERATORS AND REGULAR EXPRESSIONS - ANSWER
==========================================

This guide covers PowerShell regular expressions, pattern matching,
and text transformation techniques.


================================================================================
                     REGEX BASICS IN POWERSHELL
================================================================================

Key operators:
    -match       Test if pattern matches (returns $true/$false)
    -notmatch    Test if pattern does NOT match
    -replace     Replace pattern with new text
    -split       Split string by pattern

Automatic variable:
    $Matches     Contains match results after -match operator

Common patterns:
    \w           Word character (letter, digit, underscore)
    \d           Digit (0-9)
    \s           Whitespace
    +            One or more
    *            Zero or more
    ?            Zero or one
    ^            Start of string
    $            End of string
    [a-z]        Character range
    ()           Capture group

================================================================================


TASK 1: Find Lowercase Letters Joined with Underscore or Dash
--------------------------------------------------------------

Create task1.ps1:

    param([string]$inputString)

    if ($inputString -match '[a-z]+[-_][a-z]+') {
        $Matches[0]
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: task1.ps1 script content                          |
|  Expected: Regex pattern for lowercase-dash-lowercase    |
+----------------------------------------------------------+

Pattern explanation:

    [a-z]+      One or more lowercase letters
    [-_]        Either dash or underscore
    [a-z]+      One or more lowercase letters

Test Example 1:

    .\task1.ps1 "A Balrog is a powerful fictional monster in Middle-earth"

Expected output:

    Middle-earth

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: task1.ps1 execution with Middle-earth             |
|  Expected: Returns Middle-earth                          |
+----------------------------------------------------------+

Test Example 2:

    .\task1.ps1 "The symbol underscore, also called low_line or low dash."

Expected output:

    low_line

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: task1.ps1 execution with low_line                 |
|  Expected: Returns low_line                              |
+----------------------------------------------------------+

Alternative - Find all matches:

    param([string]$inputString)

    $pattern = '[a-z]+[-_][a-z]+'
    [regex]::Matches($inputString, $pattern) | ForEach-Object { $_.Value }

Extended pattern (includes uppercase start):

    param([string]$inputString)

    if ($inputString -match '[A-Za-z]*[a-z]+[-_][a-z]+') {
        $Matches[0]
    }


TASK 2: Match Word at End of String
------------------------------------

Create task2.ps1:

    param([string]$inputString)

    if ($inputString -match '\w+$') {
        $Matches[0]
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: task2.ps1 script content                          |
|  Expected: Regex pattern for word at end                 |
+----------------------------------------------------------+

Pattern explanation:

    \w+         One or more word characters
    $           End of string anchor

Test:

    .\task2.ps1 "Ents or talking trees are derived from the Old English word for giant"

Expected output:

    giant

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: task2.ps1 execution                               |
|  Expected: Returns giant                                 |
+----------------------------------------------------------+

Alternative patterns:

    # Only letters at end
    if ($inputString -match '[a-zA-Z]+$') { $Matches[0] }

    # Using split (last element)
    ($inputString -split '\s+')[-1]

    # Remove punctuation first
    if ($inputString.TrimEnd('.!?') -match '\w+$') { $Matches[0] }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Alternative solutions                             |
|  Expected: Different approaches                          |
+----------------------------------------------------------+


TASK 3: Match IP Address
-------------------------

Create task3.ps1:

    param([string]$inputString)

    if ($inputString -match '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}') {
        $Matches[0]
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: task3.ps1 script content                          |
|  Expected: IP address regex pattern                      |
+----------------------------------------------------------+

Pattern explanation:

    \d{1,3}     1 to 3 digits
    \.          Literal dot (escaped)
    \d{1,3}     1 to 3 digits (repeated 3 more times)

Test:

    .\task3.ps1 "Reply from 10.8.216.77: bytes=32 time<1ms TTL=128"

Expected output:

    10.8.216.77

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: task3.ps1 execution                               |
|  Expected: Returns 10.8.216.77                           |
+----------------------------------------------------------+

Alternative - More strict IP validation:

    param([string]$inputString)

    # Pattern that validates 0-255 range
    $pattern = '\b(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\b'

    if ($inputString -match $pattern) {
        $Matches[0]
    }

Simplified pattern:

    param([string]$inputString)

    if ($inputString -match '\b(\d+\.){3}\d+\b') {
        $Matches[0]
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: IP validation alternatives                        |
|  Expected: Different regex approaches                    |
+----------------------------------------------------------+


TASK 4: Convert Date Format mm-dd-yyyy to dd-mm-yyyy
-----------------------------------------------------

Create task4.ps1:

    param([string]$inputString)

    $inputString -replace '(\d{2})-(\d{2})-(\d{4})', '$2-$1-$3'

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: task4.ps1 script content                          |
|  Expected: Date conversion with capture groups           |
+----------------------------------------------------------+

Pattern explanation:

    (\d{2})     Capture group 1: month (2 digits)
    -           Literal dash
    (\d{2})     Capture group 2: day (2 digits)
    -           Literal dash
    (\d{4})     Capture group 3: year (4 digits)

    '$2-$1-$3'  Replacement: day-month-year

Test:

    .\task4.ps1 "09-17-1991"

Expected output:

    17-09-1991

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: task4.ps1 execution                               |
|  Expected: Returns 17-09-1991                            |
+----------------------------------------------------------+

Alternative using named groups:

    param([string]$inputString)

    if ($inputString -match '(?<month>\d{2})-(?<day>\d{2})-(?<year>\d{4})') {
        "$($Matches['day'])-$($Matches['month'])-$($Matches['year'])"
    }

Using DateTime parsing:

    param([string]$inputString)

    $date = [DateTime]::ParseExact($inputString, "MM-dd-yyyy", $null)
    $date.ToString("dd-MM-yyyy")

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Alternative date conversion methods               |
|  Expected: Named groups and DateTime approaches          |
+----------------------------------------------------------+


TASK 5: Find Words At Least 6 Characters Long
-----------------------------------------------

Create task5.ps1:

    param([string]$inputString)

    [regex]::Matches($inputString, '\b[a-zA-Z]{6,}\b') | ForEach-Object { $_.Value }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: task5.ps1 script content                          |
|  Expected: Regex for 6+ character words                  |
+----------------------------------------------------------+

Pattern explanation:

    \b          Word boundary
    [a-zA-Z]    Letter characters only
    {6,}        6 or more occurrences
    \b          Word boundary

Test:

    .\task5.ps1 "Tue Sep 15 2020 15:39:48 GMT+0300 (Belarus Local Time)"

Expected output:

    Belarus

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: task5.ps1 execution                               |
|  Expected: Returns Belarus                               |
+----------------------------------------------------------+

Alternative - Using Select-String:

    param([string]$inputString)

    $inputString | Select-String -Pattern '\b[a-zA-Z]{6,}\b' -AllMatches |
        ForEach-Object { $_.Matches.Value }

Alternative - Using split and filter:

    param([string]$inputString)

    $inputString -split '\W+' | Where-Object { $_.Length -ge 6 -and $_ -match '^[a-zA-Z]+$' }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Alternative word filtering methods                |
|  Expected: Different approaches                          |
+----------------------------------------------------------+

Test with multiple long words:

    .\task5.ps1 "PowerShell scripting enables automation"

Expected output:

    PowerShell
    scripting
    enables
    automation


TASK 6: Remove Multiple Spaces
-------------------------------

Create task6.ps1:

    param([string]$inputString)

    ($inputString -replace '\s+', ' ').Trim()

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: task6.ps1 script content                          |
|  Expected: Multiple space removal pattern                |
+----------------------------------------------------------+

Pattern explanation:

    \s+         One or more whitespace characters
    ' '         Replace with single space
    .Trim()     Remove leading/trailing spaces

Test:

    .\task6.ps1 " kube-system   coredns-869cb84759-drhbg                     1/1     Running   0          4h5m"

Expected output:

    kube-system coredns-869cb84759-drhbg 1/1 Running 0 4h5m

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: task6.ps1 execution                               |
|  Expected: Clean single-spaced output                    |
+----------------------------------------------------------+

Alternative approaches:

    # Using regex with explicit spaces
    param([string]$inputString)
    ($inputString -replace '  +', ' ').Trim()

    # Using split and join
    param([string]$inputString)
    ($inputString.Trim() -split '\s+') -join ' '

    # Using .NET Regex
    param([string]$inputString)
    [regex]::Replace($inputString.Trim(), '\s{2,}', ' ')

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: Alternative space removal methods                 |
|  Expected: Different approaches                          |
+----------------------------------------------------------+


COMPLETE SCRIPT FILES
---------------------

All scripts in one location:

task1.ps1:
    param([string]$inputString)
    if ($inputString -match '[a-z]+[-_][a-z]+') { $Matches[0] }

task2.ps1:
    param([string]$inputString)
    if ($inputString -match '\w+$') { $Matches[0] }

task3.ps1:
    param([string]$inputString)
    if ($inputString -match '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}') { $Matches[0] }

task4.ps1:
    param([string]$inputString)
    $inputString -replace '(\d{2})-(\d{2})-(\d{4})', '$2-$1-$3'

task5.ps1:
    param([string]$inputString)
    [regex]::Matches($inputString, '\b[a-zA-Z]{6,}\b') | ForEach-Object { $_.Value }

task6.ps1:
    param([string]$inputString)
    ($inputString -replace '\s+', ' ').Trim()

+----------------------------------------------------------+
|                    >>> SCREENSHOT 19 <<<                  |
|                                                          |
|  Show: All script files created                          |
|  Expected: Six .ps1 files in directory                   |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 20 <<<                  |
|                                                          |
|  Show: All tasks tested successfully                     |
|  Expected: Complete test run of all scripts              |
+----------------------------------------------------------+


================================================================================
                     REGEX PATTERN REFERENCE
================================================================================

| Pattern     | Description                    | Example                       |
|-------------|--------------------------------|-------------------------------|
| .           | Any character except newline   | a.c matches "abc"             |
| \d          | Digit [0-9]                    | \d+ matches "123"             |
| \D          | Non-digit                      | \D+ matches "abc"             |
| \w          | Word char [a-zA-Z0-9_]         | \w+ matches "hello_1"         |
| \W          | Non-word character             | \W matches "!"                |
| \s          | Whitespace                     | \s+ matches "   "             |
| \S          | Non-whitespace                 | \S+ matches "text"            |
| \b          | Word boundary                  | \bword\b exact match          |
| ^           | Start of string                | ^Start matches "Start..."     |
| $           | End of string                  | end$ matches "...end"         |
| [abc]       | Character class                | [aeiou] matches vowels        |
| [^abc]      | Negated class                  | [^0-9] matches non-digits     |
| [a-z]       | Range                          | [a-z]+ matches lowercase      |
| *           | Zero or more                   | ab*c matches "ac", "abc"      |
| +           | One or more                    | ab+c matches "abc", "abbc"    |
| ?           | Zero or one                    | ab?c matches "ac", "abc"      |
| {n}         | Exactly n times                | \d{3} matches "123"           |
| {n,}        | n or more times                | \d{3,} matches "1234"         |
| {n,m}       | Between n and m times          | \d{2,4} matches "12", "1234"  |
| (...)       | Capture group                  | (\d+) captures digits         |
| (?:...)     | Non-capturing group            | (?:\d+) groups without capture|
| \1, \2      | Backreference                  | (\w)\1 matches "aa", "bb"     |
| $1, $2      | Replacement reference          | -replace '(a)', '$1$1'        |

================================================================================


================================================================================
                     POWERSHELL REGEX OPERATORS
================================================================================

| Operator    | Description                    | Example                       |
|-------------|--------------------------------|-------------------------------|
| -match      | Pattern match (case-insensitive)| "Hello" -match 'h'           |
| -cmatch     | Case-sensitive match           | "Hello" -cmatch 'H'           |
| -notmatch   | Pattern does not match         | "Hello" -notmatch '\d'        |
| -replace    | Replace pattern                | "abc" -replace 'b', 'x'       |
| -creplace   | Case-sensitive replace         | "ABC" -creplace 'A', 'x'      |
| -split      | Split by pattern               | "a-b-c" -split '-'            |

================================================================================


================================================================================
                     $MATCHES AUTOMATIC VARIABLE
================================================================================

After -match, $Matches contains:

    $Matches[0]     Full match
    $Matches[1]     First capture group
    $Matches[2]     Second capture group
    $Matches['name'] Named capture group

Example:

    "John Smith 30" -match '(\w+)\s(\w+)\s(\d+)'
    $Matches[0]     # "John Smith 30"
    $Matches[1]     # "John"
    $Matches[2]     # "Smith"
    $Matches[3]     # "30"

Named groups:

    "John Smith 30" -match '(?<first>\w+)\s(?<last>\w+)\s(?<age>\d+)'
    $Matches['first']  # "John"
    $Matches['last']   # "Smith"
    $Matches['age']    # "30"

================================================================================


================================================================================
                     [REGEX] CLASS METHODS
================================================================================

| Method                   | Description                              |
|--------------------------|------------------------------------------|
| [regex]::Match()         | Returns first match                      |
| [regex]::Matches()       | Returns all matches                      |
| [regex]::Replace()       | Replace with pattern                     |
| [regex]::Split()         | Split by pattern                         |
| [regex]::IsMatch()       | Test if pattern matches                  |

Examples:

    # Find all matches
    [regex]::Matches("a1b2c3", '\d') | ForEach-Object { $_.Value }
    # Returns: 1, 2, 3

    # Replace with callback
    [regex]::Replace("hello", '.', { $args[0].Value.ToUpper() })
    # Returns: HELLO

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: Pattern doesn't match expected text
Solution: Test pattern in isolation, check escaping
    "test" -match 'test'    # Simple test first
    "a.b" -match 'a\.b'     # Escape special chars

Problem: $Matches is empty or null
Solution: $Matches only populated after successful -match
    if ($string -match $pattern) { $Matches[0] }

Problem: Backslash issues in pattern
Solution: Use single quotes or escape properly
    'path\\file'      # Single quotes (literal)
    "path\\\\file"    # Double quotes (escape twice)

Problem: -replace removes too much
Solution: Make pattern more specific, use anchors
    "aaa" -replace 'a+', 'b'     # "b" (greedy)
    "aaa" -replace 'a', 'b'      # "bbb" (each)

Problem: Case sensitivity issues
Solution: Use -cmatch/-creplace for case-sensitive
    "ABC" -match 'abc'    # True (case-insensitive)
    "ABC" -cmatch 'abc'   # False (case-sensitive)

Problem: Capture groups not working in -replace
Solution: Use single quotes around replacement string
    'ab' -replace '(a)(b)', '$2$1'    # Correct: "ba"
    "ab" -replace "(a)(b)", "$2$1"    # May not work as expected

Problem: Script not accepting arguments
Solution: Add param() block at top of script
    param([string]$inputString)
    # Rest of script

+----------------------------------------------------------+
|                    >>> SCREENSHOT 21 <<<                  |
|                                                          |
|  Show: Summary of all regex tasks                        |
|  Expected: All six patterns working correctly            |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Task 1: Pattern [a-z]+[-_][a-z]+ finds dash/underscore words
[ ] Task 1: "Middle-earth" and "low_line" correctly matched
[ ] Task 2: Pattern \w+$ matches word at end of string
[ ] Task 2: "giant" correctly extracted
[ ] Task 3: IP address pattern \d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3} works
[ ] Task 3: "10.8.216.77" correctly matched
[ ] Task 4: Capture groups swap date format correctly
[ ] Task 4: "09-17-1991" becomes "17-09-1991"
[ ] Task 5: Pattern \b[a-zA-Z]{6,}\b finds 6+ char words
[ ] Task 5: "Belarus" correctly found
[ ] Task 6: Pattern \s+ replaces multiple spaces with single
[ ] Task 6: Output is clean single-spaced string
[ ] Understand -match operator and $Matches variable
[ ] Understand -replace operator with capture groups
[ ] Know how to use [regex]::Matches() for multiple matches
[ ] Scripts accept command line arguments with param()

================================================================================
