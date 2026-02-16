# FUNDAMENTALS OF POWERSHELL FUNCTIONS - ANSWER

This guide covers PowerShell function creation, parameters,
switch parameters, and pipeline input handling.


## POWERSHELL FUNCTIONS OVERVIEW

Basic function syntax:

```powershell
function FunctionName {
    param(
        [type]$ParameterName
    )
    # Function body
}
```

Advanced function syntax:

```powershell
function FunctionName {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [string]$Name,

        [switch]$Force
    )

    begin { }      # Runs once at start
    process { }    # Runs for each pipeline item
    end { }        # Runs once at end
}
```

Parameter types:

```
[string]      Text
[int]         Integer
[bool]        Boolean
[switch]      Switch (flag)
[array]       Array
[hashtable]   Hash table
```


## TASK 1: Write-ColoredMessage Function with Switch Parameters

Goal: Create function with switch parameters for different colors.

Solution - Write-ColoredMessage function:

```powershell
function Write-ColoredMessage {
    param(
        [Parameter(Mandatory=$true)]
        [string]$Text,

        [switch]$Red,
        [switch]$Blue,
        [switch]$Green,
        [switch]$Yellow,
        [switch]$Cyan,
        [switch]$Magenta,
        [switch]$White
    )

    # Determine which color was selected
    $color = "White"  # Default

    if ($Red)     { $color = "Red" }
    if ($Blue)    { $color = "Blue" }
    if ($Green)   { $color = "Green" }
    if ($Yellow)  { $color = "Yellow" }
    if ($Cyan)    { $color = "Cyan" }
    if ($Magenta) { $color = "Magenta" }
    if ($White)   { $color = "White" }

    # Output colored text
    Write-Host $Text -ForegroundColor $color
}
```

> **Screenshot 1**
>
> Show: Write-ColoredMessage function definition
> Expected: Function with switch parameters

Test Example 1 - Red color:

```powershell
Write-ColoredMessage -Text "Hello there!" -Red
```

Expected output (in red):

```
Hello there!
```

> **Screenshot 2**
>
> Show: Red colored output
> Expected: "Hello there!" in red

Test Example 2 - Blue color:

```powershell
Write-ColoredMessage -Text "Hello there!" -Blue
```

Expected output (in blue):

```
Hello there!
```

> **Screenshot 3**
>
> Show: Blue colored output
> Expected: "Hello there!" in blue

Test all colors:

```powershell
Write-ColoredMessage -Text "This is RED" -Red
Write-ColoredMessage -Text "This is BLUE" -Blue
Write-ColoredMessage -Text "This is GREEN" -Green
Write-ColoredMessage -Text "This is YELLOW" -Yellow
Write-ColoredMessage -Text "This is CYAN" -Cyan
Write-ColoredMessage -Text "This is MAGENTA" -Magenta
```

> **Screenshot 4**
>
> Show: All colors tested
> Expected: Each line in different color

Alternative using switch statement:

```powershell
function Write-ColoredMessage {
    param(
        [Parameter(Mandatory=$true)]
        [string]$Text,

        [switch]$Red,
        [switch]$Blue,
        [switch]$Green,
        [switch]$Yellow,
        [switch]$Cyan,
        [switch]$Magenta,
        [switch]$White
    )

    $color = switch ($true) {
        $Red     { "Red"; break }
        $Blue    { "Blue"; break }
        $Green   { "Green"; break }
        $Yellow  { "Yellow"; break }
        $Cyan    { "Cyan"; break }
        $Magenta { "Magenta"; break }
        $White   { "White"; break }
        default  { "White" }
    }

    Write-Host $Text -ForegroundColor $color
}
```

> **Screenshot 5**
>
> Show: Switch statement alternative
> Expected: Same functionality, cleaner code


## TASK 2: Get-ValueFromPipe Function with Pipeline Input

Goal: Create function that accepts and processes pipeline input.

Solution - Get-ValueFromPipe function:

```powershell
function Get-ValueFromPipe {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$true)]
        $InputObject
    )

    process {
        Write-Host "Value from pipe: $InputObject"
    }
}
```

> **Screenshot 6**
>
> Show: Get-ValueFromPipe function definition
> Expected: Function with pipeline parameter

Test Example 1 - Numbers:

```powershell
1..3 | Get-ValueFromPipe
```

Expected output:

```
Value from pipe: 1
Value from pipe: 2
Value from pipe: 3
```

> **Screenshot 7**
>
> Show: Numbers through pipeline
> Expected: Three lines with values 1, 2, 3

Test Example 2 - Strings:

```powershell
@("First", "Second") | Get-ValueFromPipe
```

Expected output:

```
Value from pipe: First
Value from pipe: Second
```

> **Screenshot 8**
>
> Show: Strings through pipeline
> Expected: Two lines with First and Second

Additional tests:

```powershell
# Single value
"Hello" | Get-ValueFromPipe
# Output: Value from pipe: Hello

# Range of numbers
1..5 | Get-ValueFromPipe

# From cmdlet output
Get-Process | Select-Object -First 3 -ExpandProperty Name | Get-ValueFromPipe
```

> **Screenshot 9**
>
> Show: Various pipeline inputs
> Expected: All values formatted correctly

Understanding begin/process/end blocks:

```powershell
function Get-ValueFromPipe {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$true)]
        $InputObject
    )

    begin {
        Write-Host "=== Starting pipeline processing ===" -ForegroundColor Cyan
        $count = 0
    }

    process {
        $count++
        Write-Host "Value from pipe: $InputObject"
    }

    end {
        Write-Host "=== Processed $count items ===" -ForegroundColor Cyan
    }
}
```

Test:

```powershell
1..3 | Get-ValueFromPipe
```

Output:

```
=== Starting pipeline processing ===
Value from pipe: 1
Value from pipe: 2
Value from pipe: 3
=== Processed 3 items ===
```

> **Screenshot 10**
>
> Show: Begin/process/end blocks
> Expected: Header, values, footer


## COMPLETE FUNCTION DEFINITIONS

Write-ColoredMessage (Task 1):

```powershell
function Write-ColoredMessage {
    <#
    .SYNOPSIS
        Displays text in specified color.
    .DESCRIPTION
        Outputs text to console with the color specified by switch parameter.
    .PARAMETER Text
        The text to display.
    .PARAMETER Red
        Display text in red color.
    .PARAMETER Blue
        Display text in blue color.
    .EXAMPLE
        Write-ColoredMessage -Text "Hello!" -Red
    #>
    param(
        [Parameter(Mandatory=$true)]
        [string]$Text,

        [switch]$Red,
        [switch]$Blue,
        [switch]$Green,
        [switch]$Yellow,
        [switch]$Cyan,
        [switch]$Magenta,
        [switch]$White
    )

    $color = switch ($true) {
        $Red     { "Red"; break }
        $Blue    { "Blue"; break }
        $Green   { "Green"; break }
        $Yellow  { "Yellow"; break }
        $Cyan    { "Cyan"; break }
        $Magenta { "Magenta"; break }
        default  { "White" }
    }

    Write-Host $Text -ForegroundColor $color
}
```

Get-ValueFromPipe (Task 2):

```powershell
function Get-ValueFromPipe {
    <#
    .SYNOPSIS
        Displays values received from pipeline.
    .DESCRIPTION
        Accepts any value from the pipeline and displays it with a prefix.
    .PARAMETER InputObject
        The value received from pipeline.
    .EXAMPLE
        1..3 | Get-ValueFromPipe
    .EXAMPLE
        @("First", "Second") | Get-ValueFromPipe
    #>
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$true)]
        $InputObject
    )

    process {
        Write-Host "Value from pipe: $InputObject"
    }
}
```

> **Screenshot 11**
>
> Show: Both complete functions with help
> Expected: Functions with comment-based help


## FUNCTION PARAMETER TYPES

| Type          | Syntax                          | Example                  |
|---------------|--------------------------------|--------------------------|
| Mandatory     | [Parameter(Mandatory=$true)]    | Required parameter       |
| Position      | [Parameter(Position=0)]         | Positional argument      |
| Pipeline      | [Parameter(ValueFromPipeline)]  | Accept pipeline input    |
| ByProperty    | [Parameter(ValueFromPipelineByPropertyName)] | Property binding |
| Switch        | [switch]$Name                   | Boolean flag             |
| ValidateSet   | [ValidateSet("A","B")]          | Allowed values           |
| ValidateRange | [ValidateRange(1,100)]          | Number range             |
| Alias         | [Alias('Name')]                 | Alternative name         |


## SWITCH PARAMETERS

Switch parameters are boolean flags:

```powershell
function Test-Switch {
    param(
        [switch]$Force,
        [switch]$WhatIf
    )

    if ($Force) {
        Write-Host "Force mode enabled"
    }

    if ($WhatIf) {
        Write-Host "WhatIf mode - no changes made"
    }
}
```

Usage:

```powershell
Test-Switch -Force           # Force is $true
Test-Switch -Force:$false    # Explicitly set to $false
Test-Switch                  # Force is $false (default)
```


## PIPELINE INPUT

Pipeline parameter attributes:

```powershell
[Parameter(ValueFromPipeline=$true)]
    # Accepts entire object from pipeline

[Parameter(ValueFromPipelineByPropertyName=$true)]
    # Binds to matching property name

[Parameter(ValueFromPipeline=$true, ValueFromPipelineByPropertyName=$true)]
    # Accepts both
```

Function blocks for pipeline:

```powershell
function Process-Pipeline {
    param(
        [Parameter(ValueFromPipeline)]
        $InputObject
    )

    begin {
        # Runs ONCE before any pipeline items
    }

    process {
        # Runs for EACH pipeline item
        # $InputObject contains current item
    }

    end {
        # Runs ONCE after all pipeline items
    }
}
```


## TROUBLESHOOTING

Problem: Function not recognized
Solution: Ensure function is defined before calling

```powershell
# Define function first
function My-Function { }
# Then call it
My-Function
```

Problem: Pipeline input not working
Solution: Add ValueFromPipeline attribute and use process block

```powershell
param([Parameter(ValueFromPipeline)]$Input)
process { $Input }
```

Problem: Multiple switch parameters active
Solution: Check for exclusive switches

```powershell
if (($Red -and $Blue) -or ($Red -and $Green)) {
    Write-Error "Only one color switch allowed"
    return
}
```

Problem: Function output not appearing
Solution: Use Write-Host for console or return for pipeline

```powershell
Write-Host "Display text"    # Console only
return $value                # Pipeline output
Write-Output $value          # Pipeline output
```

Problem: Pipeline processes all at once
Solution: Use process block, not just function body

```powershell
process { }   # Runs for each item
# vs
end { }       # Runs once with all items
```

> **Screenshot 12**
>
> Show: Summary of function tasks
> Expected: Both tasks completed successfully


## SELF-REVIEW CHECKLIST

- [ ] Task 1: Function named Write-ColoredMessage
- [ ] Task 1: -Text parameter accepts the message
- [ ] Task 1: Switch parameters: -Red, -Blue, -Green, etc.
- [ ] Task 1: Write-Host used with -ForegroundColor
- [ ] Task 1: Each switch produces correct color output
- [ ] Task 2: Function named Get-ValueFromPipe
- [ ] Task 2: [Parameter(ValueFromPipeline=$true)] used
- [ ] Task 2: process block handles each pipeline item
- [ ] Task 2: Output format is "Value from pipe: X"
- [ ] Task 2: Works with 1..3 range operator
- [ ] Task 2: Works with @("First", "Second") array
- [ ] Understand function syntax (function Name { })
- [ ] Understand param() block for parameters
- [ ] Understand [switch] parameter type
- [ ] Understand begin/process/end blocks
- [ ] Know difference between Write-Host and Write-Output
