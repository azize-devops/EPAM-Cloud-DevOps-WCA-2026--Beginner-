ADVANCED FUNCTIONS AND SCRIPTS - ANSWER
========================================

This guide covers advanced PowerShell functions with CmdletBinding,
parameter validation, SupportsShouldProcess, and error handling.


================================================================================
                     ADVANCED FUNCTIONS OVERVIEW
================================================================================

Advanced function template:

    function Verb-Noun {
        [CmdletBinding(SupportsShouldProcess=$true)]
        param(
            [Parameter(Mandatory=$true, Position=0, ValueFromPipeline=$true)]
            [ValidateNotNullOrEmpty()]
            [string]$Path,

            [Parameter()]
            [ValidateRange(1, 1000000)]
            [int]$Size = 100
        )

        begin {
            # Initialization
        }

        process {
            # Main logic
            if ($PSCmdlet.ShouldProcess($target, $action)) {
                # Perform action
            }
        }

        end {
            # Cleanup
        }
    }

Key features:
    [CmdletBinding()]           Enable advanced function features
    SupportsShouldProcess       Enable -WhatIf and -Confirm
    Validate* attributes        Parameter validation
    $PSCmdlet.ShouldProcess()   Confirm before action

================================================================================


TASK 1: Get-FileSizeReport - Show Files Sorted by Size
--------------------------------------------------------

Goal: Advanced function to list files with sizes, sorted by size.

Complete Get-FileSizeReport function:

    function Get-FileSizeReport {
        <#
        .SYNOPSIS
            Shows the size of files in a specific folder sorted by size.

        .DESCRIPTION
            Lists all files in the specified folder with their sizes,
            sorted by file size in ascending or descending order.

        .PARAMETER Path
            The folder path to analyze. Defaults to current directory.

        .PARAMETER Descending
            Sort files by size in descending order (largest first).

        .PARAMETER Recurse
            Include files in subdirectories.

        .PARAMETER Top
            Show only top N files.

        .EXAMPLE
            Get-FileSizeReport -Path "C:\Windows\Temp"

        .EXAMPLE
            Get-FileSizeReport -Path "C:\Logs" -Descending -Top 10
        #>

        [CmdletBinding()]
        param(
            [Parameter(Mandatory=$false, Position=0, ValueFromPipeline=$true)]
            [ValidateScript({
                if (Test-Path $_ -PathType Container) { $true }
                else { throw "Path '$_' is not a valid directory." }
            })]
            [string]$Path = (Get-Location).Path,

            [Parameter()]
            [switch]$Descending,

            [Parameter()]
            [switch]$Recurse,

            [Parameter()]
            [ValidateRange(1, 10000)]
            [int]$Top
        )

        begin {
            Write-Verbose "Starting file size analysis..."
            Write-Verbose "Target folder: $Path"
        }

        process {
            # Get files from the folder
            $getChildItemParams = @{
                Path = $Path
                File = $true
                ErrorAction = 'SilentlyContinue'
            }

            if ($Recurse) {
                $getChildItemParams['Recurse'] = $true
                Write-Verbose "Including subdirectories..."
            }

            $files = Get-ChildItem @getChildItemParams

            if (-not $files) {
                Write-Warning "No files found in '$Path'"
                return
            }

            Write-Verbose "Found $($files.Count) files"

            # Create file size report
            $report = $files | ForEach-Object {
                [PSCustomObject]@{
                    Name = $_.Name
                    'Size (Bytes)' = $_.Length
                    'Size (KB)' = [math]::Round($_.Length / 1KB, 2)
                    'Size (MB)' = [math]::Round($_.Length / 1MB, 2)
                    LastModified = $_.LastWriteTime
                    FullPath = $_.FullName
                }
            }

            # Sort by size
            if ($Descending) {
                $report = $report | Sort-Object 'Size (Bytes)' -Descending
                Write-Verbose "Sorted by size (descending)"
            }
            else {
                $report = $report | Sort-Object 'Size (Bytes)'
                Write-Verbose "Sorted by size (ascending)"
            }

            # Limit to top N if specified
            if ($Top) {
                $report = $report | Select-Object -First $Top
                Write-Verbose "Limited to top $Top files"
            }

            # Output report
            $report
        }

        end {
            # Summary
            if ($files) {
                $totalSize = ($files | Measure-Object -Property Length -Sum).Sum
                $totalSizeMB = [math]::Round($totalSize / 1MB, 2)

                Write-Verbose "Total files: $($files.Count)"
                Write-Verbose "Total size: $totalSizeMB MB"
            }
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Get-FileSizeReport function definition            |
|  Expected: Complete advanced function with help          |
+----------------------------------------------------------+

Test Example 1 - Basic usage:

    Get-FileSizeReport -Path "C:\Windows\Temp"

Expected output:

    Name                Size (Bytes)  Size (KB)  Size (MB)  LastModified         FullPath
    ----                ------------  ---------  ---------  ------------         --------
    small.tmp                   1024       1.00       0.00  1/15/2024 10:00 AM   C:\Windows\Temp\small.tmp
    medium.log                 51200      50.00       0.05  1/15/2024 11:00 AM   C:\Windows\Temp\medium.log
    large.dat               5242880    5120.00       5.00  1/15/2024 12:00 PM   C:\Windows\Temp\large.dat

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Basic usage output                                |
|  Expected: Files sorted by size ascending                |
+----------------------------------------------------------+

Test Example 2 - Descending with top 10:

    Get-FileSizeReport -Path "C:\Windows\System32" -Descending -Top 10

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Top 10 largest files                              |
|  Expected: Largest files first                           |
+----------------------------------------------------------+

Test Example 3 - With verbose output:

    Get-FileSizeReport -Path "C:\Temp" -Recurse -Verbose

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Verbose output with subdirectories                |
|  Expected: Verbose messages and recursive scan           |
+----------------------------------------------------------+

Alternative - Formatted output:

    function Get-FileSizeReport {
        [CmdletBinding()]
        param(
            [Parameter(Mandatory=$false)]
            [ValidateScript({ Test-Path $_ -PathType Container })]
            [string]$Path = ".",

            [switch]$Descending,
            [switch]$Recurse
        )

        $files = Get-ChildItem -Path $Path -File -Recurse:$Recurse

        Write-Host "`nFile Size Report: $Path" -ForegroundColor Cyan
        Write-Host ("=" * 60) -ForegroundColor Cyan

        $sortedFiles = if ($Descending) {
            $files | Sort-Object Length -Descending
        } else {
            $files | Sort-Object Length
        }

        $sortedFiles | ForEach-Object {
            $sizeKB = [math]::Round($_.Length / 1KB, 2)
            $sizeMB = [math]::Round($_.Length / 1MB, 2)
            "{0,-40} {1,10} KB ({2,8} MB)" -f $_.Name, $sizeKB, $sizeMB
        }

        Write-Host ("=" * 60) -ForegroundColor Cyan
        $totalMB = [math]::Round(($files | Measure-Object Length -Sum).Sum / 1MB, 2)
        Write-Host "Total: $($files.Count) files, $totalMB MB" -ForegroundColor Green
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Formatted console output                          |
|  Expected: Clean formatted report                        |
+----------------------------------------------------------+


TASK 2: Remove-LargeFiles - Delete Files Larger Than Threshold
----------------------------------------------------------------

Goal: Advanced function to delete files larger than specified size.

Complete Remove-LargeFiles function:

    function Remove-LargeFiles {
        <#
        .SYNOPSIS
            Deletes files larger than specified size from a folder.

        .DESCRIPTION
            Removes files from the specified folder if their size exceeds
            the threshold in KB. Supports -WhatIf and -Confirm for safety.

        .PARAMETER Path
            The folder path to process.

        .PARAMETER SizeKB
            The size threshold in kilobytes. Files larger than this will be deleted.

        .PARAMETER Recurse
            Include files in subdirectories.

        .PARAMETER Force
            Skip confirmation prompts (use with caution).

        .EXAMPLE
            Remove-LargeFiles -Path "C:\Temp" -SizeKB 1024
            Deletes files larger than 1 MB from C:\Temp

        .EXAMPLE
            Remove-LargeFiles -Path "C:\Logs" -SizeKB 500 -WhatIf
            Shows what would be deleted without actually deleting
        #>

        [CmdletBinding(SupportsShouldProcess=$true, ConfirmImpact='High')]
        param(
            [Parameter(Mandatory=$true, Position=0, ValueFromPipeline=$true)]
            [ValidateScript({
                if (Test-Path $_ -PathType Container) { $true }
                else { throw "Path '$_' is not a valid directory." }
            })]
            [string]$Path,

            [Parameter(Mandatory=$true, Position=1)]
            [ValidateRange(1, [int]::MaxValue)]
            [Alias('Size', 'ThresholdKB')]
            [int]$SizeKB,

            [Parameter()]
            [switch]$Recurse,

            [Parameter()]
            [switch]$Force
        )

        begin {
            Write-Verbose "Starting large file removal process..."
            Write-Verbose "Target folder: $Path"
            Write-Verbose "Size threshold: $SizeKB KB"

            $deletedCount = 0
            $deletedSize = 0
            $errorCount = 0
        }

        process {
            # Get files from the folder
            $getChildItemParams = @{
                Path = $Path
                File = $true
                ErrorAction = 'SilentlyContinue'
            }

            if ($Recurse) {
                $getChildItemParams['Recurse'] = $true
                Write-Verbose "Including subdirectories..."
            }

            $files = Get-ChildItem @getChildItemParams

            if (-not $files) {
                Write-Warning "No files found in '$Path'"
                return
            }

            # Filter files larger than threshold
            $thresholdBytes = $SizeKB * 1KB
            $largeFiles = $files | Where-Object { $_.Length -gt $thresholdBytes }

            if (-not $largeFiles) {
                Write-Host "No files found larger than $SizeKB KB" -ForegroundColor Green
                return
            }

            Write-Host "`nFiles larger than $SizeKB KB:" -ForegroundColor Yellow
            Write-Host ("-" * 50)

            foreach ($file in $largeFiles) {
                $fileSizeKB = [math]::Round($file.Length / 1KB, 2)
                $fileSizeMB = [math]::Round($file.Length / 1MB, 2)

                # Show file info
                Write-Host "$($file.Name) - $fileSizeKB KB ($fileSizeMB MB)" -ForegroundColor White

                # ShouldProcess provides -WhatIf and -Confirm support
                $target = $file.FullName
                $action = "Delete file ($fileSizeKB KB)"

                if ($PSCmdlet.ShouldProcess($target, $action)) {
                    try {
                        Remove-Item -Path $file.FullName -Force:$Force -ErrorAction Stop
                        Write-Verbose "Deleted: $($file.FullName)"
                        $deletedCount++
                        $deletedSize += $file.Length
                    }
                    catch {
                        Write-Error "Failed to delete '$($file.Name)': $_"
                        $errorCount++
                    }
                }
            }
        }

        end {
            Write-Host ("-" * 50)
            Write-Host "`nSummary:" -ForegroundColor Cyan

            if ($WhatIfPreference) {
                Write-Host "WhatIf mode - no files were deleted" -ForegroundColor Yellow
                Write-Host "Would delete $($largeFiles.Count) file(s)"
            }
            else {
                $deletedSizeMB = [math]::Round($deletedSize / 1MB, 2)
                Write-Host "Deleted: $deletedCount file(s), $deletedSizeMB MB freed" -ForegroundColor Green

                if ($errorCount -gt 0) {
                    Write-Host "Errors: $errorCount file(s) could not be deleted" -ForegroundColor Red
                }
            }
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Remove-LargeFiles function definition             |
|  Expected: Advanced function with SupportsShouldProcess  |
+----------------------------------------------------------+

Test Example 1 - WhatIf mode (preview):

    Remove-LargeFiles -Path "C:\Temp" -SizeKB 1024 -WhatIf

Expected output:

    Files larger than 1024 KB:
    --------------------------------------------------
    largefile.zip - 5120 KB (5 MB)
    What if: Performing the operation "Delete file (5120 KB)" on target "C:\Temp\largefile.zip".
    backup.dat - 2048 KB (2 MB)
    What if: Performing the operation "Delete file (2048 KB)" on target "C:\Temp\backup.dat".
    --------------------------------------------------

    Summary:
    WhatIf mode - no files were deleted
    Would delete 2 file(s)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: WhatIf mode output                                |
|  Expected: Preview without actual deletion               |
+----------------------------------------------------------+

Test Example 2 - With confirmation:

    Remove-LargeFiles -Path "C:\Temp" -SizeKB 500 -Confirm

Expected output:

    Files larger than 500 KB:
    --------------------------------------------------
    largefile.zip - 5120 KB (5 MB)

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Delete file (5120 KB)" on target "C:\Temp\largefile.zip".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Confirmation prompt                               |
|  Expected: Interactive confirmation                      |
+----------------------------------------------------------+

Test Example 3 - Actual deletion:

    Remove-LargeFiles -Path "C:\Temp" -SizeKB 1024

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Actual file deletion                              |
|  Expected: Files deleted with summary                    |
+----------------------------------------------------------+

Test Example 4 - Verbose with recursion:

    Remove-LargeFiles -Path "C:\Logs" -SizeKB 100 -Recurse -Verbose

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Recursive deletion with verbose                   |
|  Expected: Detailed progress messages                    |
+----------------------------------------------------------+

Alternative - Simplified version:

    function Remove-LargeFiles {
        [CmdletBinding(SupportsShouldProcess)]
        param(
            [Parameter(Mandatory)]
            [ValidateScript({ Test-Path $_ })]
            [string]$Path,

            [Parameter(Mandatory)]
            [int]$SizeKB
        )

        $thresholdBytes = $SizeKB * 1KB

        Get-ChildItem -Path $Path -File |
            Where-Object { $_.Length -gt $thresholdBytes } |
            ForEach-Object {
                $sizeInfo = "$([math]::Round($_.Length/1KB, 2)) KB"

                if ($PSCmdlet.ShouldProcess($_.FullName, "Delete ($sizeInfo)")) {
                    Remove-Item $_.FullName -Force
                    Write-Host "Deleted: $($_.Name) ($sizeInfo)" -ForegroundColor Yellow
                }
            }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Simplified version                                |
|  Expected: Compact working function                      |
+----------------------------------------------------------+


COMPLETE FUNCTION DEFINITIONS
-----------------------------

Get-FileSizeReport (Task 1):

    function Get-FileSizeReport {
        [CmdletBinding()]
        param(
            [Parameter(Position=0)]
            [ValidateScript({ Test-Path $_ -PathType Container })]
            [string]$Path = ".",

            [switch]$Descending,
            [switch]$Recurse,

            [ValidateRange(1, 10000)]
            [int]$Top
        )

        $files = Get-ChildItem -Path $Path -File -Recurse:$Recurse

        $report = $files | ForEach-Object {
            [PSCustomObject]@{
                Name = $_.Name
                'Size (KB)' = [math]::Round($_.Length / 1KB, 2)
                'Size (MB)' = [math]::Round($_.Length / 1MB, 2)
                LastModified = $_.LastWriteTime
                FullPath = $_.FullName
            }
        }

        $sorted = $report | Sort-Object 'Size (KB)' -Descending:$Descending

        if ($Top) { $sorted | Select-Object -First $Top }
        else { $sorted }
    }

Remove-LargeFiles (Task 2):

    function Remove-LargeFiles {
        [CmdletBinding(SupportsShouldProcess=$true, ConfirmImpact='High')]
        param(
            [Parameter(Mandatory=$true)]
            [ValidateScript({ Test-Path $_ -PathType Container })]
            [string]$Path,

            [Parameter(Mandatory=$true)]
            [ValidateRange(1, [int]::MaxValue)]
            [int]$SizeKB,

            [switch]$Recurse
        )

        $thresholdBytes = $SizeKB * 1KB

        Get-ChildItem -Path $Path -File -Recurse:$Recurse |
            Where-Object { $_.Length -gt $thresholdBytes } |
            ForEach-Object {
                $sizeKB = [math]::Round($_.Length / 1KB, 2)
                if ($PSCmdlet.ShouldProcess($_.FullName, "Delete file ($sizeKB KB)")) {
                    Remove-Item $_.FullName -Force
                    Write-Host "Deleted: $($_.Name) - $sizeKB KB" -ForegroundColor Yellow
                }
            }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Both complete functions                           |
|  Expected: Compact versions of both functions            |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Both functions tested                             |
|  Expected: Successful execution                          |
+----------------------------------------------------------+


================================================================================
                     CMDLETBINDING ATTRIBUTES
================================================================================

| Attribute              | Description                              |
|------------------------|------------------------------------------|
| SupportsShouldProcess  | Enable -WhatIf and -Confirm              |
| ConfirmImpact          | 'Low', 'Medium', 'High', 'None'          |
| DefaultParameterSetName| Default parameter set                    |
| SupportsPaging         | Enable -First, -Skip, -IncludeTotalCount |
| PositionalBinding      | Enable/disable positional parameters     |

Example:

    [CmdletBinding(
        SupportsShouldProcess=$true,
        ConfirmImpact='High',
        DefaultParameterSetName='ByPath'
    )]

================================================================================


================================================================================
                     PARAMETER VALIDATION ATTRIBUTES
================================================================================

| Attribute              | Description                    | Example             |
|------------------------|--------------------------------|---------------------|
| ValidateNotNull        | Cannot be $null                | [ValidateNotNull()] |
| ValidateNotNullOrEmpty | Cannot be $null or empty       | [ValidateNotNullOrEmpty()] |
| ValidateSet            | Must be in set                 | [ValidateSet("A","B")] |
| ValidateRange          | Must be in range               | [ValidateRange(1,100)] |
| ValidateLength         | String length range            | [ValidateLength(1,50)] |
| ValidateCount          | Array element count            | [ValidateCount(1,5)]  |
| ValidatePattern        | Must match regex               | [ValidatePattern("^\d+")] |
| ValidateScript         | Custom validation              | [ValidateScript({...})] |

Examples:

    # Path must exist
    [ValidateScript({ Test-Path $_ })]
    [string]$Path

    # Must be valid email
    [ValidatePattern('^[\w.-]+@[\w.-]+\.\w+$')]
    [string]$Email

    # Must be positive number
    [ValidateRange(1, [int]::MaxValue)]
    [int]$Size

================================================================================


================================================================================
                     SHOULDPROCESS PATTERNS
================================================================================

Basic pattern:

    if ($PSCmdlet.ShouldProcess($target, $action)) {
        # Perform action
    }

With explicit messages:

    if ($PSCmdlet.ShouldProcess(
        $target,           # Target resource
        $action,           # Action to perform
        $warning           # Warning message
    )) {
        # Perform action
    }

Multiple operations:

    foreach ($item in $items) {
        if ($PSCmdlet.ShouldProcess($item.Name, "Delete")) {
            Remove-Item $item.FullName
        }
    }

Force parameter pattern:

    if ($Force -or $PSCmdlet.ShouldProcess($target, $action)) {
        # Perform action
    }

================================================================================


================================================================================
                     ERROR HANDLING IN ADVANCED FUNCTIONS
================================================================================

Try-Catch pattern:

    try {
        Remove-Item $path -ErrorAction Stop
        Write-Verbose "Successfully deleted $path"
    }
    catch {
        Write-Error "Failed to delete $path: $_"
        # or
        $PSCmdlet.WriteError($_)
    }

Terminating vs Non-terminating errors:

    # Non-terminating (continues)
    Write-Error "Something went wrong"

    # Terminating (stops)
    throw "Critical error"
    $PSCmdlet.ThrowTerminatingError($errorRecord)

ErrorAction handling:

    Get-ChildItem -Path $path -ErrorAction SilentlyContinue
    Get-ChildItem -Path $path -ErrorAction Stop  # Throw on error

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: ShouldProcess not prompting
Solution: Ensure CmdletBinding has SupportsShouldProcess
    [CmdletBinding(SupportsShouldProcess=$true)]

Problem: -WhatIf not working
Solution: Use $PSCmdlet.ShouldProcess() for each action
    if ($PSCmdlet.ShouldProcess($target, $action)) { }

Problem: ValidateScript error message unclear
Solution: Throw descriptive error in script block
    [ValidateScript({
        if (Test-Path $_) { $true }
        else { throw "Path '$_' does not exist" }
    })]

Problem: Parameters not binding correctly
Solution: Check Parameter attributes and types
    [Parameter(Mandatory=$true, Position=0)]
    [string]$Path

Problem: Function not showing in Get-Command
Solution: Dot-source the script or add to module
    . .\MyFunctions.ps1
    Get-Command Get-FileSizeReport

Problem: Verbose messages not appearing
Solution: Use -Verbose switch when calling function
    Get-FileSizeReport -Path "C:\Temp" -Verbose

Problem: ConfirmImpact not triggering confirmation
Solution: Set $ConfirmPreference or use ConfirmImpact='High'
    $ConfirmPreference = 'Low'  # Confirm on Low impact
    [CmdletBinding(ConfirmImpact='High')]

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Summary of advanced function tasks                |
|  Expected: Both tasks completed successfully             |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Task 1: Function named Get-FileSizeReport
[ ] Task 1: [CmdletBinding()] attribute added
[ ] Task 1: Path parameter with validation
[ ] Task 1: Files sorted by size
[ ] Task 1: Size displayed in KB/MB
[ ] Task 1: Supports -Descending switch
[ ] Task 2: Function named Remove-LargeFiles
[ ] Task 2: SupportsShouldProcess=$true added
[ ] Task 2: SizeKB parameter with ValidateRange
[ ] Task 2: $PSCmdlet.ShouldProcess() used for deletion
[ ] Task 2: -WhatIf shows preview without deleting
[ ] Task 2: -Confirm prompts before each deletion
[ ] Task 2: Summary shows deleted count and freed space
[ ] Understand CmdletBinding attributes
[ ] Know how to use ValidateScript
[ ] Understand ShouldProcess pattern
[ ] Know difference between -WhatIf and -Confirm

================================================================================
