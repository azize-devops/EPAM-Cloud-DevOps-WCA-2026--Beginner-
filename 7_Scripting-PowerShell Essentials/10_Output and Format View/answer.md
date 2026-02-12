# OUTPUT AND FORMAT VIEW - ANSWER

This guide covers PowerShell output formatting, custom views,
and verbose/debug output control.


================================================================================
                     OUTPUT FORMATTING BASICS
================================================================================

Format cmdlets:
    Format-Table     Display as table (default for many cmdlets)
    Format-List      Display as property list
    Format-Wide      Display single property in columns
    Format-Custom    Display using custom view

Output cmdlets:
    Out-Host         Send to console (default)
    Out-File         Send to file
    Out-String       Convert to string
    Out-GridView     Display in GUI grid (Windows only)

Write cmdlets:
    Write-Host       Write to console (not pipeline)
    Write-Output     Write to pipeline (default)
    Write-Verbose    Write verbose message (-Verbose)
    Write-Debug      Write debug message (-Debug)
    Write-Warning    Write warning message
    Write-Error      Write error message

================================================================================


TASK 1: List Directory with File Sizes in Megabytes
-----------------------------------------------------

Goal: Replicate Get-ChildItem output with file size in MB.

Standard Get-ChildItem output:

    Get-ChildItem C:\Windows\System32 -File | Select-Object -First 5

Output format:
    Mode                 LastWriteTime         Length Name
    ----                 -------------         ------ ----
    -a----         1/15/2024   9:30 AM        1234567 file.dll

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Standard Get-ChildItem output                     |
|  Expected: Default file listing format                   |
+----------------------------------------------------------+

Solution - script1.ps1:

    param(
        [string]$Path = "."
    )

    Get-ChildItem -Path $Path -File |
        Select-Object Mode, LastWriteTime,
            @{Name='Size(MB)'; Expression={
                [math]::Round($_.Length / 1MB, 2)
            }},
            Name |
        Format-Table -AutoSize

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: script1.ps1 content                               |
|  Expected: Script with calculated Size(MB) property      |
+----------------------------------------------------------+

Test the script:

    .\script1.ps1 -Path "C:\Windows\System32"

Expected output:

    Mode   LastWriteTime          Size(MB) Name
    ----   -------------          -------- ----
    -a---- 1/15/2024  9:30:15 AM     1.23 file1.dll
    -a---- 1/10/2024  2:45:30 PM     0.56 file2.exe
    -a---- 1/05/2024 11:20:00 AM    15.78 file3.dll

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Script output with MB sizes                       |
|  Expected: File sizes in megabytes                       |
+----------------------------------------------------------+

Enhanced version with more details:

    param(
        [string]$Path = ".",
        [switch]$Recurse
    )

    $files = if ($Recurse) {
        Get-ChildItem -Path $Path -File -Recurse
    } else {
        Get-ChildItem -Path $Path -File
    }

    # Display directory header (like Get-ChildItem)
    $resolvedPath = (Resolve-Path $Path).Path
    Write-Host "`n    Directory: $resolvedPath`n" -ForegroundColor Cyan

    $files |
        Select-Object Mode,
            @{N='LastWriteTime'; E={$_.LastWriteTime.ToString('g')}},
            @{N='Size(MB)'; E={'{0,10:N2}' -f ($_.Length / 1MB)}},
            Name |
        Format-Table -AutoSize

    # Summary
    $totalSize = ($files | Measure-Object -Property Length -Sum).Sum / 1MB
    Write-Host "`nTotal: $($files.Count) files, $([math]::Round($totalSize, 2)) MB" -ForegroundColor Green

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Enhanced version with header and summary          |
|  Expected: Directory header and total size               |
+----------------------------------------------------------+

Alternative with Format-List:

    param([string]$Path = ".")

    Get-ChildItem -Path $Path -File |
        ForEach-Object {
            [PSCustomObject]@{
                Name = $_.Name
                'Size (MB)' = [math]::Round($_.Length / 1MB, 2)
                LastWriteTime = $_.LastWriteTime
                Attributes = $_.Attributes
                FullPath = $_.FullName
            }
        } |
        Format-List

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Format-List alternative                           |
|  Expected: Detailed property list view                   |
+----------------------------------------------------------+

Complete script with multiple size formats:

    param(
        [string]$Path = ".",
        [ValidateSet('Bytes', 'KB', 'MB', 'GB')]
        [string]$SizeUnit = 'MB'
    )

    $divisor = switch ($SizeUnit) {
        'Bytes' { 1 }
        'KB'    { 1KB }
        'MB'    { 1MB }
        'GB'    { 1GB }
    }

    Get-ChildItem -Path $Path -File |
        Select-Object Mode, LastWriteTime,
            @{N="Size($SizeUnit)"; E={[math]::Round($_.Length / $divisor, 2)}},
            Name |
        Format-Table -AutoSize

Usage:

    .\script1.ps1 -Path "C:\Windows" -SizeUnit KB
    .\script1.ps1 -Path "C:\Windows" -SizeUnit GB

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Multi-unit size script                            |
|  Expected: Flexible size unit parameter                  |
+----------------------------------------------------------+


TASK 2: Bubble Sort with Verbose Output
----------------------------------------

Goal: Implement bubble sort with step-by-step verbose output.

Bubble Sort Algorithm:
    1. Compare adjacent elements
    2. Swap if they are in wrong order
    3. Repeat until no swaps needed

Create script2.ps1:

    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [int[]]$Array
    )

    # Copy array to avoid modifying original
    $arr = $Array.Clone()
    $n = $arr.Length
    $pass = 0

    Write-Verbose "Starting Bubble Sort with $n elements"
    Write-Verbose "Initial array: $($arr -join ', ')"

    for ($i = 0; $i -lt $n - 1; $i++) {
        $swapped = $false
        $pass++

        Write-Verbose "`n--- Pass $pass ---"

        for ($j = 0; $j -lt $n - $i - 1; $j++) {
            Write-Verbose "Comparing: $($arr[$j]) and $($arr[$j + 1])"

            if ($arr[$j] -gt $arr[$j + 1]) {
                # Swap elements
                $temp = $arr[$j]
                $arr[$j] = $arr[$j + 1]
                $arr[$j + 1] = $temp
                $swapped = $true

                Write-Verbose "  Swapped! Array now: $($arr -join ', ')"
            }
        }

        Write-Verbose "After pass $pass : $($arr -join ', ')"

        # If no swaps occurred, array is sorted
        if (-not $swapped) {
            Write-Verbose "No swaps in pass $pass - Array is sorted!"
            break
        }
    }

    Write-Verbose "`nSorting complete in $pass passes"

    # Return sorted array
    return $arr

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: script2.ps1 content                               |
|  Expected: Bubble sort with Write-Verbose                |
+----------------------------------------------------------+

Test without Verbose (silent):

    .\script2.ps1 -Array 64, 34, 25, 12, 22, 11, 90

Expected output:

    11
    12
    22
    25
    34
    64
    90

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Script without -Verbose                           |
|  Expected: Only final sorted result                      |
+----------------------------------------------------------+

Test with Verbose (detailed):

    .\script2.ps1 -Array 64, 34, 25, 12, 22, 11, 90 -Verbose

Expected output:

    VERBOSE: Starting Bubble Sort with 7 elements
    VERBOSE: Initial array: 64, 34, 25, 12, 22, 11, 90
    VERBOSE:
    VERBOSE: --- Pass 1 ---
    VERBOSE: Comparing: 64 and 34
    VERBOSE:   Swapped! Array now: 34, 64, 25, 12, 22, 11, 90
    VERBOSE: Comparing: 64 and 25
    VERBOSE:   Swapped! Array now: 34, 25, 64, 12, 22, 11, 90
    VERBOSE: Comparing: 64 and 12
    VERBOSE:   Swapped! Array now: 34, 25, 12, 64, 22, 11, 90
    ...
    VERBOSE: Sorting complete in 6 passes
    11
    12
    22
    25
    34
    64
    90

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Script with -Verbose flag                         |
|  Expected: Step-by-step sorting visible                  |
+----------------------------------------------------------+

Enhanced version with color output:

    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [int[]]$Array,
        [switch]$ShowSteps
    )

    $arr = $Array.Clone()
    $n = $arr.Length
    $pass = 0

    if ($ShowSteps -or $VerbosePreference -eq 'Continue') {
        Write-Host "Starting Bubble Sort" -ForegroundColor Cyan
        Write-Host "Initial: $($arr -join ', ')" -ForegroundColor Yellow
    }

    for ($i = 0; $i -lt $n - 1; $i++) {
        $swapped = $false
        $pass++

        Write-Verbose "=== Pass $pass ==="

        for ($j = 0; $j -lt $n - $i - 1; $j++) {
            Write-Verbose "  Comparing [$j]:$($arr[$j]) with [$($j+1)]:$($arr[$j+1])"

            if ($arr[$j] -gt $arr[$j + 1]) {
                $temp = $arr[$j]
                $arr[$j] = $arr[$j + 1]
                $arr[$j + 1] = $temp
                $swapped = $true

                Write-Verbose "    SWAP -> $($arr -join ', ')"
            }
        }

        if (-not $swapped) { break }
    }

    if ($ShowSteps -or $VerbosePreference -eq 'Continue') {
        Write-Host "Sorted:  $($arr -join ', ')" -ForegroundColor Green
        Write-Host "Completed in $pass passes" -ForegroundColor Cyan
    }

    return $arr

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Enhanced version with colors                      |
|  Expected: Colorful step output                          |
+----------------------------------------------------------+

Alternative with progress bar:

    [CmdletBinding()]
    param(
        [int[]]$Array
    )

    $arr = $Array.Clone()
    $n = $arr.Length
    $totalComparisons = ($n * ($n - 1)) / 2
    $comparison = 0

    for ($i = 0; $i -lt $n - 1; $i++) {
        $swapped = $false

        for ($j = 0; $j -lt $n - $i - 1; $j++) {
            $comparison++
            $percent = [math]::Round(($comparison / $totalComparisons) * 100)

            Write-Progress -Activity "Bubble Sort" `
                -Status "Pass $($i+1): Comparing $($arr[$j]) and $($arr[$j+1])" `
                -PercentComplete $percent

            Write-Verbose "[$percent%] Comparing $($arr[$j]) and $($arr[$j+1])"

            if ($arr[$j] -gt $arr[$j + 1]) {
                $temp = $arr[$j]
                $arr[$j] = $arr[$j + 1]
                $arr[$j + 1] = $temp
                $swapped = $true

                Write-Verbose "  Swapped -> $($arr -join ', ')"
            }

            Start-Sleep -Milliseconds 100  # Slow down for visibility
        }

        if (-not $swapped) { break }
    }

    Write-Progress -Activity "Bubble Sort" -Completed
    return $arr

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Version with progress bar                         |
|  Expected: Write-Progress during sort                    |
+----------------------------------------------------------+

Function version for module use:

    function Sort-BubbleSort {
        [CmdletBinding()]
        param(
            [Parameter(Mandatory, ValueFromPipeline)]
            [int[]]$InputArray
        )

        begin {
            $allItems = @()
        }

        process {
            $allItems += $InputArray
        }

        end {
            $arr = $allItems.Clone()
            $n = $arr.Length

            Write-Verbose "Sorting $n elements with Bubble Sort"

            for ($i = 0; $i -lt $n - 1; $i++) {
                $swapped = $false

                for ($j = 0; $j -lt $n - $i - 1; $j++) {
                    if ($arr[$j] -gt $arr[$j + 1]) {
                        $arr[$j], $arr[$j + 1] = $arr[$j + 1], $arr[$j]
                        $swapped = $true
                        Write-Verbose "Pass $($i+1): Swapped $($arr[$j+1]) <-> $($arr[$j])"
                    }
                }

                if (-not $swapped) {
                    Write-Verbose "Array sorted after $($i+1) passes"
                    break
                }
            }

            return $arr
        }
    }

    # Usage
    Sort-BubbleSort -InputArray 5, 2, 8, 1, 9 -Verbose
    # or via pipeline
    5, 2, 8, 1, 9 | Sort-BubbleSort -Verbose

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Function with pipeline support                    |
|  Expected: Reusable function                             |
+----------------------------------------------------------+


COMPLETE SCRIPT FILES
---------------------

script1.ps1 (Directory Listing with MB):

    param(
        [string]$Path = "."
    )

    $resolvedPath = (Resolve-Path $Path).Path
    Write-Host "`n    Directory: $resolvedPath`n" -ForegroundColor Cyan

    Get-ChildItem -Path $Path -File |
        Select-Object Mode,
            @{N='LastWriteTime'; E={$_.LastWriteTime.ToString('g')}},
            @{N='Size(MB)'; E={'{0,10:N2}' -f ($_.Length / 1MB)}},
            Name |
        Format-Table -AutoSize

    $files = Get-ChildItem -Path $Path -File
    $totalSize = ($files | Measure-Object -Property Length -Sum).Sum / 1MB
    Write-Host "`nTotal: $($files.Count) file(s), $([math]::Round($totalSize, 2)) MB" -ForegroundColor Green

script2.ps1 (Bubble Sort with Verbose):

    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [int[]]$Array
    )

    $arr = $Array.Clone()
    $n = $arr.Length
    $pass = 0

    Write-Verbose "Starting Bubble Sort with $n elements"
    Write-Verbose "Initial array: $($arr -join ', ')"

    for ($i = 0; $i -lt $n - 1; $i++) {
        $swapped = $false
        $pass++

        Write-Verbose "`n--- Pass $pass ---"

        for ($j = 0; $j -lt $n - $i - 1; $j++) {
            Write-Verbose "Comparing: $($arr[$j]) and $($arr[$j + 1])"

            if ($arr[$j] -gt $arr[$j + 1]) {
                $temp = $arr[$j]
                $arr[$j] = $arr[$j + 1]
                $arr[$j + 1] = $temp
                $swapped = $true
                Write-Verbose "  Swapped! Array now: $($arr -join ', ')"
            }
        }

        if (-not $swapped) {
            Write-Verbose "No swaps needed - Array is sorted!"
            break
        }
    }

    Write-Verbose "`nSorting complete in $pass passes"
    return $arr

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Both complete scripts                             |
|  Expected: script1.ps1 and script2.ps1                   |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Both scripts tested                               |
|  Expected: Successful execution                          |
+----------------------------------------------------------+


================================================================================
                     FORMAT CMDLETS REFERENCE
================================================================================

| Cmdlet         | Description                    | Example                    |
|----------------|--------------------------------|----------------------------|
| Format-Table   | Display as table               | | Format-Table -AutoSize   |
| Format-List    | Display as property list       | | Format-List *            |
| Format-Wide    | Display single property wide   | | Format-Wide -Column 4    |
| Format-Custom  | Custom format view             | | Format-Custom            |

Format-Table parameters:
    -AutoSize        Adjust column width to content
    -Property        Select properties to display
    -GroupBy         Group rows by property
    -Wrap            Wrap long text
    -HideTableHeaders Hide column headers

================================================================================


================================================================================
                     WRITE CMDLETS REFERENCE
================================================================================

| Cmdlet         | Stream       | Visibility Control          |
|----------------|--------------|------------------------------|
| Write-Output   | Success (1)  | Always (pipeline output)     |
| Write-Host     | Information  | Always (console only)        |
| Write-Verbose  | Verbose (4)  | -Verbose or $VerbosePreference|
| Write-Debug    | Debug (5)    | -Debug or $DebugPreference   |
| Write-Warning  | Warning (3)  | Always (yellow text)         |
| Write-Error    | Error (2)    | Always (red text)            |

Preference variables:
    $VerbosePreference = 'Continue'    # Show verbose messages
    $VerbosePreference = 'SilentlyContinue'  # Hide (default)
    $DebugPreference = 'Continue'      # Show debug messages

================================================================================


================================================================================
                     CMDLETBINDING AND COMMON PARAMETERS
================================================================================

[CmdletBinding()] enables common parameters:
    -Verbose         Show verbose messages
    -Debug           Show debug messages
    -ErrorAction     Control error handling
    -WarningAction   Control warning handling
    -OutVariable     Store output in variable
    -OutBuffer       Buffer output

Example:

    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [string]$Name
    )

    Write-Verbose "Processing $Name"
    Write-Debug "Debug info: $Name"

================================================================================


================================================================================
                     CALCULATED PROPERTIES
================================================================================

Syntax for Select-Object:
    @{Name='PropertyName'; Expression={$_.Property}}
    @{N='Short'; E={$_.Property}}   # Short form
    @{L='Label'; E={$_.Property}}   # Alternative

Examples:

    # Size in MB
    @{N='Size(MB)'; E={[math]::Round($_.Length / 1MB, 2)}}

    # Formatted date
    @{N='Date'; E={$_.LastWriteTime.ToString('yyyy-MM-dd')}}

    # Percentage
    @{N='CPU%'; E={'{0:P2}' -f ($_.CPU / 100)}}

    # Right-aligned number
    @{N='Count'; E={'{0,10:N0}' -f $_.Count}}

================================================================================


================================================================================
                     FORMAT STRINGS REFERENCE
================================================================================

| Format   | Description              | Example                       |
|----------|--------------------------|-------------------------------|
| N0       | Number, no decimals      | '{0:N0}' -f 1234567 = 1,234,567|
| N2       | Number, 2 decimals       | '{0:N2}' -f 1234.5 = 1,234.50 |
| P2       | Percentage, 2 decimals   | '{0:P2}' -f 0.1234 = 12.34%   |
| C2       | Currency, 2 decimals     | '{0:C2}' -f 99.5 = $99.50     |
| D5       | Digits, 5 places         | '{0:D5}' -f 42 = 00042        |
| X        | Hexadecimal              | '{0:X}' -f 255 = FF           |
| g        | General date/time        | $date.ToString('g')           |

Alignment:
    {0,10}   Right-align in 10 chars
    {0,-10}  Left-align in 10 chars

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: Format-Table truncates output
Solution: Use -AutoSize or -Wrap
    | Format-Table -AutoSize
    | Format-Table -Wrap

Problem: Write-Verbose not showing
Solution: Use -Verbose switch or set preference
    .\script.ps1 -Verbose
    $VerbosePreference = 'Continue'

Problem: [CmdletBinding()] not recognized
Solution: Must be first line in param block
    [CmdletBinding()]
    param(...)

Problem: Calculated property not showing
Solution: Check expression syntax
    @{N='Name'; E={$_.Property}}  # Correct
    @{Name='Name', Expression={}}  # Wrong comma

Problem: Output not formatted as expected
Solution: Format cmdlets must be last in pipeline
    Get-Process | Sort-Object | Format-Table  # Correct
    Get-Process | Format-Table | Sort-Object  # Wrong

Problem: Write-Host output not captured
Solution: Use Write-Output for pipeline data
    Write-Output $result   # Goes to pipeline
    Write-Host $result     # Console only

Problem: Array displayed vertically
Solution: Use -join or Format-Table
    $arr -join ', '
    $arr | Format-Table

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Summary of output formatting tasks                |
|  Expected: Both tasks working correctly                  |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Task 1: Script lists files in directory
[ ] Task 1: Output format matches Get-ChildItem style
[ ] Task 1: File sizes displayed in megabytes
[ ] Task 1: Calculated property used for size conversion
[ ] Task 1: Format-Table used for output
[ ] Task 2: Bubble sort algorithm correctly implemented
[ ] Task 2: [CmdletBinding()] added to script
[ ] Task 2: Write-Verbose used for step output
[ ] Task 2: -Verbose switch controls output visibility
[ ] Task 2: Without -Verbose, only result shown
[ ] Task 2: With -Verbose, each comparison/swap shown
[ ] Understand Format-Table vs Format-List
[ ] Understand Write-Verbose vs Write-Host
[ ] Know how to create calculated properties
[ ] Know how to use [CmdletBinding()]

================================================================================

