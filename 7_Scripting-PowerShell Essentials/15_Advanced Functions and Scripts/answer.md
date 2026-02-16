# ADVANCED FUNCTIONS AND SCRIPTS - ANSWER

This guide covers advanced PowerShell functions with CmdletBinding,
parameter validation, SupportsShouldProcess, and error handling.


## ADVANCED FUNCTIONS OVERVIEW

Advanced function template:

```powershell
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
```

Key features:

```
[CmdletBinding()]           Enable advanced function features
SupportsShouldProcess       Enable -WhatIf and -Confirm
Validate* attributes        Parameter validation
$PSCmdlet.ShouldProcess()   Confirm before action
```


## TASK 1: Get-FileSizeReport - Show Files Sorted by Size

Goal: Advanced function to list files with sizes, sorted by size.

Complete Get-FileSizeReport function:

```powershell
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
        }

        $files = Get-ChildItem @getChildItemParams

        if (-not $files) {
            Write-Warning "No files found in '$Path'"
            return
        }

        # Create file size report
        $report = $files | ForEach-Object {
            [PSCustomObject]@{
                Name = $_.Name
                'Size (KB)' = [math]::Round($_.Length / 1KB, 2)
                'Size (MB)' = [math]::Round($_.Length / 1MB, 2)
                LastModified = $_.LastWriteTime
                FullPath = $_.FullName
            }
        }

        # Sort by size
        if ($Descending) {
            $report = $report | Sort-Object 'Size (KB)' -Descending
        }
        else {
            $report = $report | Sort-Object 'Size (KB)'
        }

        # Limit to top N if specified
        if ($Top) {
            $report = $report | Select-Object -First $Top
        }

        # Output report
        $report
    }

    end {
        Write-Verbose "Analysis complete"
    }
}
```

> **Screenshot 1**
>
> Show: Get-FileSizeReport function definition
> Expected: Complete advanced function with help

Test Example 1 - Basic usage:

```powershell
Get-FileSizeReport -Path "C:\Windows\Temp"
```

> **Screenshot 2**
>
> Show: Basic usage output
> Expected: Files sorted by size ascending

Test Example 2 - Descending with top 10:

```powershell
Get-FileSizeReport -Path "C:\Windows\System32" -Descending -Top 10
```

> **Screenshot 3**
>
> Show: Top 10 largest files
> Expected: Largest files first

Test Example 3 - With verbose output:

```powershell
Get-FileSizeReport -Path "C:\Temp" -Recurse -Verbose
```

> **Screenshot 4**
>
> Show: Verbose output with subdirectories
> Expected: Verbose messages and recursive scan


## TASK 2: Remove-LargeFiles - Delete Files Larger Than Threshold

Goal: Advanced function to delete files larger than specified size.

Complete Remove-LargeFiles function:

```powershell
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
        [switch]$Recurse
    )

    begin {
        Write-Verbose "Starting large file removal process..."
        Write-Verbose "Target folder: $Path"
        Write-Verbose "Size threshold: $SizeKB KB"

        $deletedCount = 0
        $deletedSize = 0
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
        }

        $files = Get-ChildItem @getChildItemParams

        # Filter files larger than threshold
        $thresholdBytes = $SizeKB * 1KB
        $largeFiles = $files | Where-Object { $_.Length -gt $thresholdBytes }

        if (-not $largeFiles) {
            Write-Host "No files found larger than $SizeKB KB" -ForegroundColor Green
            return
        }

        Write-Host "`nFiles larger than $SizeKB KB:" -ForegroundColor Yellow

        foreach ($file in $largeFiles) {
            $fileSizeKB = [math]::Round($file.Length / 1KB, 2)

            # ShouldProcess provides -WhatIf and -Confirm support
            if ($PSCmdlet.ShouldProcess($file.FullName, "Delete file ($fileSizeKB KB)")) {
                try {
                    Remove-Item -Path $file.FullName -Force -ErrorAction Stop
                    Write-Host "Deleted: $($file.Name) - $fileSizeKB KB" -ForegroundColor Yellow
                    $deletedCount++
                    $deletedSize += $file.Length
                }
                catch {
                    Write-Error "Failed to delete '$($file.Name)': $_"
                }
            }
        }
    }

    end {
        $deletedSizeMB = [math]::Round($deletedSize / 1MB, 2)
        Write-Host "`nSummary: Deleted $deletedCount file(s), $deletedSizeMB MB freed" -ForegroundColor Green
    }
}
```

> **Screenshot 5**
>
> Show: Remove-LargeFiles function definition
> Expected: Advanced function with SupportsShouldProcess

Test Example 1 - WhatIf mode (preview):

```powershell
Remove-LargeFiles -Path "C:\Temp" -SizeKB 1024 -WhatIf
```

Expected output:

```
Files larger than 1024 KB:
What if: Performing the operation "Delete file (5120 KB)" on target "C:\Temp\largefile.zip".
What if: Performing the operation "Delete file (2048 KB)" on target "C:\Temp\backup.dat".

Summary: Deleted 0 file(s), 0 MB freed
```

> **Screenshot 6**
>
> Show: WhatIf mode output
> Expected: Preview without actual deletion

Test Example 2 - With confirmation:

```powershell
Remove-LargeFiles -Path "C:\Temp" -SizeKB 500 -Confirm
```

> **Screenshot 7**
>
> Show: Confirmation prompt
> Expected: Interactive confirmation

Test Example 3 - Actual deletion:

```powershell
Remove-LargeFiles -Path "C:\Temp" -SizeKB 1024
```

> **Screenshot 8**
>
> Show: Actual file deletion
> Expected: Files deleted with summary


## CMDLETBINDING ATTRIBUTES

| Attribute              | Description                              |
|------------------------|------------------------------------------|
| SupportsShouldProcess  | Enable -WhatIf and -Confirm              |
| ConfirmImpact          | 'Low', 'Medium', 'High', 'None'          |
| DefaultParameterSetName| Default parameter set                    |
| SupportsPaging         | Enable -First, -Skip, -IncludeTotalCount |

Example:

```powershell
[CmdletBinding(
    SupportsShouldProcess=$true,
    ConfirmImpact='High'
)]
```


## PARAMETER VALIDATION ATTRIBUTES

| Attribute              | Description                    | Example             |
|------------------------|--------------------------------|---------------------|
| ValidateNotNull        | Cannot be $null                | [ValidateNotNull()] |
| ValidateNotNullOrEmpty | Cannot be $null or empty       | [ValidateNotNullOrEmpty()] |
| ValidateSet            | Must be in set                 | [ValidateSet("A","B")] |
| ValidateRange          | Must be in range               | [ValidateRange(1,100)] |
| ValidateLength         | String length range            | [ValidateLength(1,50)] |
| ValidatePattern        | Must match regex               | [ValidatePattern("^\d+")] |
| ValidateScript         | Custom validation              | [ValidateScript({...})] |

Examples:

```powershell
# Path must exist
[ValidateScript({ Test-Path $_ })]
[string]$Path

# Must be valid email
[ValidatePattern('^[\w.-]+@[\w.-]+\.\w+$')]
[string]$Email

# Must be positive number
[ValidateRange(1, [int]::MaxValue)]
[int]$Size
```


## SHOULDPROCESS PATTERNS

Basic pattern:

```powershell
if ($PSCmdlet.ShouldProcess($target, $action)) {
    # Perform action
}
```

Multiple operations:

```powershell
foreach ($item in $items) {
    if ($PSCmdlet.ShouldProcess($item.Name, "Delete")) {
        Remove-Item $item.FullName
    }
}
```


## ERROR HANDLING IN ADVANCED FUNCTIONS

Try-Catch pattern:

```powershell
try {
    Remove-Item $path -ErrorAction Stop
    Write-Verbose "Successfully deleted $path"
}
catch {
    Write-Error "Failed to delete $path: $_"
}
```

ErrorAction handling:

```powershell
Get-ChildItem -Path $path -ErrorAction SilentlyContinue
Get-ChildItem -Path $path -ErrorAction Stop  # Throw on error
```


## TROUBLESHOOTING

Problem: ShouldProcess not prompting
Solution: Ensure CmdletBinding has SupportsShouldProcess

```powershell
[CmdletBinding(SupportsShouldProcess=$true)]
```

Problem: -WhatIf not working
Solution: Use $PSCmdlet.ShouldProcess() for each action

```powershell
if ($PSCmdlet.ShouldProcess($target, $action)) { }
```

Problem: ValidateScript error message unclear
Solution: Throw descriptive error in script block

```powershell
[ValidateScript({
    if (Test-Path $_) { $true }
    else { throw "Path '$_' does not exist" }
})]
```

Problem: Verbose messages not appearing
Solution: Use -Verbose switch when calling function

```powershell
Get-FileSizeReport -Path "C:\Temp" -Verbose
```

> **Screenshot 9**
>
> Show: Summary of advanced function tasks
> Expected: Both tasks completed successfully


## SELF-REVIEW CHECKLIST

- [ ] Task 1: Function named Get-FileSizeReport
- [ ] Task 1: [CmdletBinding()] attribute added
- [ ] Task 1: Path parameter with validation
- [ ] Task 1: Files sorted by size
- [ ] Task 1: Size displayed in KB/MB
- [ ] Task 1: Supports -Descending switch
- [ ] Task 2: Function named Remove-LargeFiles
- [ ] Task 2: SupportsShouldProcess=$true added
- [ ] Task 2: SizeKB parameter with ValidateRange
- [ ] Task 2: $PSCmdlet.ShouldProcess() used for deletion
- [ ] Task 2: -WhatIf shows preview without deleting
- [ ] Task 2: -Confirm prompts before each deletion
- [ ] Task 2: Summary shows deleted count and freed space
- [ ] Understand CmdletBinding attributes
- [ ] Know how to use ValidateScript
- [ ] Understand ShouldProcess pattern
- [ ] Know difference between -WhatIf and -Confirm
