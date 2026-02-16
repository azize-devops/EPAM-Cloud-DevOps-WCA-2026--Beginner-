# OUTPUT AND FORMAT VIEW - ANSWER

This guide covers PowerShell output formatting, including Format cmdlets,
Out cmdlets, and customizing how data is displayed.


## OUTPUT AND FORMATTING OVERVIEW

Format cmdlets:

| Cmdlet         | Description                    | Example                    |
|----------------|--------------------------------|----------------------------|
| Format-Table   | Display as table               | Format-Table -AutoSize     |
| Format-List    | Display as list                | Format-List *              |
| Format-Wide    | Display single property wide   | Format-Wide -Column 3      |
| Format-Custom  | Custom formatting              | Format-Custom              |

Out cmdlets:

| Cmdlet         | Description                    | Example                    |
|----------------|--------------------------------|----------------------------|
| Out-File       | Output to file                 | Out-File "output.txt"      |
| Out-String     | Convert to string              | Out-String                 |
| Out-GridView   | Display in GUI grid            | Out-GridView               |
| Out-Host       | Output to console              | Out-Host                   |
| Out-Null       | Discard output                 | Out-Null                   |


## TASK 1: Format-Table Options

Goal: Display process data in table format with various options.

Basic Format-Table:

```powershell
Get-Process | Select-Object Name, Id, CPU -First 10 | Format-Table
```

> **Screenshot 1**
>
> Show: Basic Format-Table
> Expected: Default table output

AutoSize for better column widths:

```powershell
Get-Process | Select-Object Name, Id, CPU -First 10 | Format-Table -AutoSize
```

> **Screenshot 2**
>
> Show: Format-Table -AutoSize
> Expected: Columns fit content

Wrap long content:

```powershell
Get-Service | Select-Object Name, DisplayName, Status | Format-Table -Wrap
```

> **Screenshot 3**
>
> Show: Format-Table -Wrap
> Expected: Long text wrapped

Custom column definitions:

```powershell
Get-Process | Format-Table -Property @(
    @{Label='Process Name'; Expression={$_.Name}; Width=20},
    @{Label='PID'; Expression={$_.Id}; Width=10},
    @{Label='Memory (MB)'; Expression={[math]::Round($_.WorkingSet64/1MB,2)}; Width=15; Alignment='Right'}
) -AutoSize
```

> **Screenshot 4**
>
> Show: Custom column definitions
> Expected: Custom labels and formatting


## TASK 2: Format-List for Detailed View

Goal: Display detailed object properties.

All properties:

```powershell
Get-Process | Select-Object -First 1 | Format-List *
```

> **Screenshot 5**
>
> Show: Format-List with all properties
> Expected: All properties listed

Selected properties:

```powershell
Get-Service -Name "WinRM" | Format-List Name, DisplayName, Status, StartType
```

Expected output:

```
Name        : WinRM
DisplayName : Windows Remote Management (WS-Management)
Status      : Running
StartType   : Automatic
```

> **Screenshot 6**
>
> Show: Format-List selected properties
> Expected: Clean property listing

Multiple objects:

```powershell
Get-Service | Where-Object Status -eq "Running" |
    Select-Object -First 3 |
    Format-List Name, Status, StartType
```

> **Screenshot 7**
>
> Show: Format-List multiple objects
> Expected: Each object as separate list


## TASK 3: Format-Wide

Goal: Display single property across multiple columns.

Process names in columns:

```powershell
Get-Process | Format-Wide -Property Name -Column 4
```

> **Screenshot 8**
>
> Show: Format-Wide with columns
> Expected: Names in 4 columns

Service names:

```powershell
Get-Service | Where-Object Status -eq "Running" | Format-Wide -Column 3
```

> **Screenshot 9**
>
> Show: Running services wide
> Expected: Services in 3 columns


## TASK 4: Out-File and Output Options

Goal: Direct output to files and other destinations.

Output to text file:

```powershell
Get-Process | Select-Object Name, Id, CPU |
    Format-Table -AutoSize |
    Out-File "processes.txt"
```

> **Screenshot 10**
>
> Show: Out-File command
> Expected: File created

Append to file:

```powershell
Get-Date | Out-File "log.txt" -Append
Get-Process | Select-Object Name -First 5 | Out-File "log.txt" -Append
```

> **Screenshot 11**
>
> Show: Append to file
> Expected: Content appended

With encoding:

```powershell
Get-Service | Out-File "services.txt" -Encoding UTF8
```

Output width:

```powershell
Get-Process | Format-Table | Out-File "wide.txt" -Width 200
```

> **Screenshot 12**
>
> Show: Output with encoding and width
> Expected: Properly formatted file


## TASK 5: Out-GridView (GUI)

Goal: Display data in interactive grid (Windows only).

Basic grid view:

```powershell
Get-Process | Out-GridView
```

> **Screenshot 13**
>
> Show: Out-GridView window
> Expected: GUI grid displayed

With selection:

```powershell
$selected = Get-Process | Out-GridView -PassThru -Title "Select Processes"
```

> **Screenshot 14**
>
> Show: Out-GridView with selection
> Expected: Selected items returned


## TASK 6: Custom Output Formatting

Goal: Create custom formatted output.

Using Write-Host with colors:

```powershell
Get-Service | ForEach-Object {
    $color = if ($_.Status -eq "Running") { "Green" } else { "Red" }
    Write-Host "$($_.Name): $($_.Status)" -ForegroundColor $color
}
```

> **Screenshot 15**
>
> Show: Colored output
> Expected: Green for running, red for stopped

Progress bar:

```powershell
$items = 1..100
for ($i = 0; $i -lt $items.Count; $i++) {
    Write-Progress -Activity "Processing" -Status "Item $i" -PercentComplete ($i / $items.Count * 100)
    Start-Sleep -Milliseconds 50
}
Write-Progress -Activity "Processing" -Completed
```

> **Screenshot 16**
>
> Show: Progress bar
> Expected: Progress indicator shown


## COMPLETE SOLUTION SCRIPT

```powershell
Write-Host "=== Format-Table ===" -ForegroundColor Cyan
Get-Process | Select-Object Name, Id, CPU -First 5 | Format-Table -AutoSize

Write-Host "`n=== Format-List ===" -ForegroundColor Cyan
Get-Service -Name "Spooler" | Format-List Name, DisplayName, Status

Write-Host "`n=== Format-Wide ===" -ForegroundColor Cyan
Get-Service | Where-Object Status -eq "Running" |
    Select-Object -First 12 |
    Format-Wide -Column 3

Write-Host "`n=== Custom Format ===" -ForegroundColor Cyan
Get-Process | Select-Object -First 5 | Format-Table @(
    @{L='Name';E={$_.Name};W=15},
    @{L='MB';E={[math]::Round($_.WorkingSet64/1MB,1)};W=10;A='Right'}
) -AutoSize

# Export to file
Get-Process |
    Select-Object Name, Id, @{N='MB';E={[math]::Round($_.WorkingSet64/1MB,2)}} -First 10 |
    Format-Table -AutoSize |
    Out-File "process_report.txt"

Write-Host "`nReport saved to process_report.txt" -ForegroundColor Green
```

> **Screenshot 17**
>
> Show: Complete formatting script
> Expected: All formats demonstrated


## FORMAT CMDLET COMPARISON

| Aspect           | Format-Table  | Format-List   | Format-Wide  |
|------------------|---------------|---------------|--------------|
| Best for         | Multiple objs | Single object | Single prop  |
| Properties shown | Columns       | List items    | One property |
| Width control    | Yes           | No            | Columns      |
| Default for      | Collections   | Complex objs  | Simple data  |


## FORMATTING TIPS

Order matters:

```powershell
# Good - filter, select, then format
Get-Process |
    Where-Object { $_.CPU -gt 0 } |
    Select-Object Name, CPU |
    Sort-Object CPU -Descending |
    Format-Table -AutoSize

# Bad - format breaks pipeline
Get-Process | Format-Table | Where-Object { $_.CPU -gt 0 }  # ERROR!
```

Format vs Export:

```powershell
# For display - use Format-*
Get-Process | Format-Table

# For files - use Export-Csv (not Format-*)
Get-Process | Export-Csv "data.csv" -NoTypeInformation
```


## TROUBLESHOOTING

Problem: Table columns cut off
Solution: Use -AutoSize or -Wrap

```powershell
Get-Process | Format-Table -AutoSize
Get-Process | Format-Table -Wrap
```

Problem: "Out-File : The OS handle's position is not what FileStream expected"
Solution: Close file in other applications

Problem: Format cmdlet breaks pipeline
Solution: Format cmdlets should be last in pipeline

```powershell
# Wrong:
Get-Process | Format-Table | Export-Csv  # ERROR

# Right:
Get-Process | Export-Csv "file.csv"
Get-Process | Format-Table  # Display separately
```

Problem: Out-GridView not available
Solution: Only available on Windows with GUI

```powershell
if ($host.Name -eq 'ConsoleHost' -and $IsWindows) {
    Get-Process | Out-GridView
}
```

Problem: File contains formatting characters
Solution: Use Export-Csv instead of Out-File for data

```powershell
# For data processing:
Get-Process | Export-Csv "data.csv"

# For human-readable report:
Get-Process | Format-Table | Out-File "report.txt"
```

Problem: Colors not showing in file
Solution: Write-Host colors don't transfer to files

```powershell
# Colors only work in console
Write-Host "Test" -ForegroundColor Red

# For files, use plain text
"Test" | Out-File "file.txt"
```

> **Screenshot 18**
>
> Show: Summary of formatting tasks
> Expected: All output formatting working


## SELF-REVIEW CHECKLIST

- [ ] Can use Format-Table with -AutoSize and -Wrap
- [ ] Can customize table columns with calculated properties
- [ ] Can use Format-List to show all object properties
- [ ] Can use Format-Wide for single-property display
- [ ] Can output to files with Out-File
- [ ] Knows Out-File -Append option
- [ ] Can use Out-GridView for interactive selection
- [ ] Understands Format cmdlets must be last in pipeline
- [ ] Knows difference between Format-* and Export-*
- [ ] Can create colored console output with Write-Host
- [ ] Can show progress with Write-Progress
- [ ] Understands when to use each format cmdlet
