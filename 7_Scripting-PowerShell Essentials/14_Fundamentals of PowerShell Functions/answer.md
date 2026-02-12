# FUNDAMENTALS OF POWERSHELL FUNCTIONS - ANSWER

This guide covers PowerShell function creation, parameters,
switch parameters, and pipeline input handling.


================================================================================
                     POWERSHELL FUNCTIONS OVERVIEW
================================================================================

Basic function syntax:

    function FunctionName {
        param(
            [type]$ParameterName
        )
        # Function body
    }

Advanced function syntax:

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

Parameter types:
    [string]      Text
    [int]         Integer
    [bool]        Boolean
    [switch]      Switch (flag)
    [array]       Array
    [hashtable]   Hash table

================================================================================


TASK 1: Write-ColoredMessage Function with Switch Parameters
--------------------------------------------------------------

Goal: Create function with switch parameters for different colors.

Solution - Write-ColoredMessage function:

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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Write-ColoredMessage function definition          |
|  Expected: Function with switch parameters               |
+----------------------------------------------------------+

Test Example 1 - Red color:

    Write-ColoredMessage -Text "Hello there!" -Red

Expected output (in red):

    Hello there!

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Red colored output                                |
|  Expected: "Hello there!" in red                         |
+----------------------------------------------------------+

Test Example 2 - Blue color:

    Write-ColoredMessage -Text "Hello there!" -Blue

Expected output (in blue):

    Hello there!

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Blue colored output                               |
|  Expected: "Hello there!" in blue                        |
+----------------------------------------------------------+

Test all colors:

    Write-ColoredMessage -Text "This is RED" -Red
    Write-ColoredMessage -Text "This is BLUE" -Blue
    Write-ColoredMessage -Text "This is GREEN" -Green
    Write-ColoredMessage -Text "This is YELLOW" -Yellow
    Write-ColoredMessage -Text "This is CYAN" -Cyan
    Write-ColoredMessage -Text "This is MAGENTA" -Magenta

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: All colors tested                                 |
|  Expected: Each line in different color                  |
+----------------------------------------------------------+

Alternative using switch statement:

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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Switch statement alternative                      |
|  Expected: Same functionality, cleaner code              |
+----------------------------------------------------------+

Alternative using ValidateSet parameter:

    function Write-ColoredMessage {
        param(
            [Parameter(Mandatory=$true)]
            [string]$Text,

            [ValidateSet("Red", "Blue", "Green", "Yellow", "Cyan", "Magenta", "White")]
            [string]$Color = "White"
        )

        Write-Host $Text -ForegroundColor $Color
    }

    # Usage
    Write-ColoredMessage -Text "Hello!" -Color Red
    Write-ColoredMessage -Text "Hello!" -Color Blue

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: ValidateSet alternative                           |
|  Expected: Color as string parameter                     |
+----------------------------------------------------------+

Enhanced version with background color:

    function Write-ColoredMessage {
        param(
            [Parameter(Mandatory=$true)]
            [string]$Text,

            [switch]$Red,
            [switch]$Blue,
            [switch]$Green,
            [switch]$Yellow,

            [switch]$Bold,
            [switch]$NoNewLine
        )

        $foreColor = switch ($true) {
            $Red    { "Red"; break }
            $Blue   { "Blue"; break }
            $Green  { "Green"; break }
            $Yellow { "Yellow"; break }
            default { "White" }
        }

        $params = @{
            Object = $Text
            ForegroundColor = $foreColor
        }

        if ($NoNewLine) {
            $params['NoNewLine'] = $true
        }

        Write-Host @params
    }


TASK 2: Get-ValueFromPipe Function with Pipeline Input
--------------------------------------------------------

Goal: Create function that accepts and processes pipeline input.

Solution - Get-ValueFromPipe function:

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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Get-ValueFromPipe function definition             |
|  Expected: Function with pipeline parameter              |
+----------------------------------------------------------+

Test Example 1 - Numbers:

    1..3 | Get-ValueFromPipe

Expected output:

    Value from pipe: 1
    Value from pipe: 2
    Value from pipe: 3

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Numbers through pipeline                          |
|  Expected: Three lines with values 1, 2, 3               |
+----------------------------------------------------------+

Test Example 2 - Strings:

    @("First", "Second") | Get-ValueFromPipe

Expected output:

    Value from pipe: First
    Value from pipe: Second

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Strings through pipeline                          |
|  Expected: Two lines with First and Second               |
+----------------------------------------------------------+

Additional tests:

    # Single value
    "Hello" | Get-ValueFromPipe
    # Output: Value from pipe: Hello

    # Range of numbers
    1..5 | Get-ValueFromPipe

    # Array of mixed types
    @(1, "Text", 3.14, $true) | Get-ValueFromPipe

    # From cmdlet output
    Get-Process | Select-Object -First 3 -ExpandProperty Name | Get-ValueFromPipe

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Various pipeline inputs                           |
|  Expected: All values formatted correctly                |
+----------------------------------------------------------+

Understanding begin/process/end blocks:

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

Test:

    1..3 | Get-ValueFromPipe

Output:

    === Starting pipeline processing ===
    Value from pipe: 1
    Value from pipe: 2
    Value from pipe: 3
    === Processed 3 items ===

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Begin/process/end blocks                          |
|  Expected: Header, values, footer                        |
+----------------------------------------------------------+

Alternative with formatting options:

    function Get-ValueFromPipe {
        [CmdletBinding()]
        param(
            [Parameter(ValueFromPipeline=$true, Mandatory=$true)]
            $InputObject,

            [string]$Prefix = "Value from pipe",
            [switch]$Numbered
        )

        begin {
            $index = 0
        }

        process {
            $index++
            if ($Numbered) {
                Write-Host "${Prefix} [$index]: $InputObject"
            }
            else {
                Write-Host "${Prefix}: $InputObject"
            }
        }
    }

    # Usage
    1..3 | Get-ValueFromPipe -Numbered
    # Output:
    # Value from pipe [1]: 1
    # Value from pipe [2]: 2
    # Value from pipe [3]: 3

    1..3 | Get-ValueFromPipe -Prefix "Item"
    # Output:
    # Item: 1
    # Item: 2
    # Item: 3

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Enhanced version with options                     |
|  Expected: Numbered and custom prefix                    |
+----------------------------------------------------------+

Pipeline with ValueFromPipelineByPropertyName:

    function Get-ValueFromPipe {
        [CmdletBinding()]
        param(
            [Parameter(ValueFromPipeline=$true,
                       ValueFromPipelineByPropertyName=$true)]
            [Alias('Name', 'Value')]
            $InputObject
        )

        process {
            Write-Host "Value from pipe: $InputObject"
        }
    }

    # Works with objects that have Name or Value property
    Get-Process | Select-Object -First 3 | Get-ValueFromPipe
    # Uses the Name property automatically

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: ValueFromPipelineByPropertyName                   |
|  Expected: Property-based pipeline binding               |
+----------------------------------------------------------+


COMPLETE FUNCTION DEFINITIONS
-----------------------------

Write-ColoredMessage (Task 1):

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

Get-ValueFromPipe (Task 2):

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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Both complete functions with help                 |
|  Expected: Functions with comment-based help             |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Both functions tested                             |
|  Expected: Successful execution                          |
+----------------------------------------------------------+


================================================================================
                     FUNCTION PARAMETER TYPES
================================================================================

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

================================================================================


================================================================================
                     SWITCH PARAMETERS
================================================================================

Switch parameters are boolean flags:

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

Usage:
    Test-Switch -Force           # Force is $true
    Test-Switch -Force:$false    # Explicitly set to $false
    Test-Switch                  # Force is $false (default)

Checking switch value:
    if ($Force) { }              # Check if switch is present
    if ($Force.IsPresent) { }    # Alternative check

================================================================================


================================================================================
                     PIPELINE INPUT
================================================================================

Pipeline parameter attributes:

    [Parameter(ValueFromPipeline=$true)]
        # Accepts entire object from pipeline

    [Parameter(ValueFromPipelineByPropertyName=$true)]
        # Binds to matching property name

    [Parameter(ValueFromPipeline=$true, ValueFromPipelineByPropertyName=$true)]
        # Accepts both

Function blocks for pipeline:

    function Process-Pipeline {
        param(
            [Parameter(ValueFromPipeline)]
            $InputObject
        )

        begin {
            # Runs ONCE before any pipeline items
            # Initialize variables, open connections
        }

        process {
            # Runs for EACH pipeline item
            # $InputObject contains current item
            # $_ also contains current item
        }

        end {
            # Runs ONCE after all pipeline items
            # Cleanup, final output
        }
    }

================================================================================


================================================================================
                     ADVANCED FUNCTION FEATURES
================================================================================

CmdletBinding for advanced functions:

    function Advanced-Function {
        [CmdletBinding(SupportsShouldProcess=$true)]
        param(
            [Parameter(Mandatory, Position=0)]
            [ValidateNotNullOrEmpty()]
            [string]$Name
        )

        if ($PSCmdlet.ShouldProcess($Name, "Delete")) {
            # Perform action
        }
    }

Common parameters enabled by CmdletBinding:
    -Verbose        Show verbose messages
    -Debug          Show debug messages
    -ErrorAction    Control error handling
    -WarningAction  Control warnings
    -WhatIf         Preview changes (if SupportsShouldProcess)
    -Confirm        Confirm before action (if SupportsShouldProcess)

================================================================================


================================================================================
                     COMMENT-BASED HELP
================================================================================

    function Get-Example {
        <#
        .SYNOPSIS
            Brief description of function.

        .DESCRIPTION
            Detailed description of what the function does.

        .PARAMETER Name
            Description of the Name parameter.

        .EXAMPLE
            Get-Example -Name "Test"
            Shows how to use the function.

        .NOTES
            Author: Your Name
            Date: 2024-01-15

        .LINK
            https://docs.microsoft.com
        #>
        param([string]$Name)

        # Function body
    }

View help:
    Get-Help Get-Example -Full
    Get-Help Get-Example -Examples

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: Function not recognized
Solution: Ensure function is defined before calling
    # Define function first
    function My-Function { }
    # Then call it
    My-Function

Problem: Pipeline input not working
Solution: Add ValueFromPipeline attribute and use process block
    param([Parameter(ValueFromPipeline)]$Input)
    process { $Input }

Problem: Multiple switch parameters active
Solution: Check for exclusive switches
    if (($Red -and $Blue) -or ($Red -and $Green)) {
        Write-Error "Only one color switch allowed"
        return
    }

Problem: Parameter not accepting value
Solution: Check parameter type and mandatory setting
    [Parameter(Mandatory=$true)]
    [string]$RequiredParam

Problem: Function output not appearing
Solution: Use Write-Host for console or return for pipeline
    Write-Host "Display text"    # Console only
    return $value                # Pipeline output
    Write-Output $value          # Pipeline output

Problem: Pipeline processes all at once
Solution: Use process block, not just function body
    process { }   # Runs for each item
    # vs
    end { }       # Runs once with all items

Problem: Switch default is $true
Solution: Switches are $false by default, check logic
    if ($Force) { }     # Correct - checks if switch is set
    if (!$Force) { }    # Runs when switch NOT set

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: Summary of function tasks                         |
|  Expected: Both tasks completed successfully             |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Task 1: Function named Write-ColoredMessage
[ ] Task 1: -Text parameter accepts the message
[ ] Task 1: Switch parameters: -Red, -Blue, -Green, etc.
[ ] Task 1: Write-Host used with -ForegroundColor
[ ] Task 1: Each switch produces correct color output
[ ] Task 2: Function named Get-ValueFromPipe
[ ] Task 2: [Parameter(ValueFromPipeline=$true)] used
[ ] Task 2: process block handles each pipeline item
[ ] Task 2: Output format is "Value from pipe: X"
[ ] Task 2: Works with 1..3 range operator
[ ] Task 2: Works with @("First", "Second") array
[ ] Understand function syntax (function Name { })
[ ] Understand param() block for parameters
[ ] Understand [switch] parameter type
[ ] Understand begin/process/end blocks
[ ] Know difference between Write-Host and Write-Output

================================================================================

