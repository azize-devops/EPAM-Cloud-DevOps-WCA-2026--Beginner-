# PIPELINES - ANSWER

This guide covers PowerShell pipelines, object passing, filtering,
and processing data through the pipeline.


## PIPELINES OVERVIEW

Pipeline basics:

```powershell
Command1 | Command2 | Command3
```

Key pipeline cmdlets:

| Cmdlet          | Description                    | Example                    |
|-----------------|--------------------------------|----------------------------|
| Where-Object    | Filter objects                 | Where { $_.Status -eq "Running" } |
| Select-Object   | Select properties              | Select Name, Id            |
| Sort-Object     | Sort objects                   | Sort-Object Name           |
| ForEach-Object  | Process each object            | ForEach { $_.Name }        |
| Group-Object    | Group objects                  | Group-Object Status        |
| Measure-Object  | Calculate statistics           | Measure-Object -Sum        |
| Export-Csv      | Export to CSV                  | Export-Csv file.csv        |


## TASK 1: Filter Running Processes

Goal: Get all running processes and filter by criteria.

Get processes using more than 100MB memory:

```powershell
Get-Process | Where-Object { $_.WorkingSet64 -gt 100MB }
```

> **Screenshot 1**
>
> Show: Memory filter command
> Expected: Processes over 100MB

Get top 10 processes by CPU:

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10
```

> **Screenshot 2**
>
> Show: Top 10 by CPU
> Expected: Sorted process list

Get specific properties:

```powershell
Get-Process | Select-Object Name, Id, CPU, WorkingSet64 |
    Sort-Object WorkingSet64 -Descending |
    Select-Object -First 5
```

Expected output:

```
Name            Id      CPU WorkingSet64
----            --      --- ------------
chrome       1234   125.34    524288000
firefox      2345    89.12    412516352
code         3456    56.78    312475648
...
```

> **Screenshot 3**
>
> Show: Formatted process output
> Expected: Selected properties displayed


## TASK 2: Service Pipeline Operations

Goal: Work with Windows services using pipelines.

Get running services:

```powershell
Get-Service | Where-Object { $_.Status -eq "Running" }
```

> **Screenshot 4**
>
> Show: Running services
> Expected: Filtered service list

Count services by status:

```powershell
Get-Service | Group-Object Status
```

Expected output:

```
Count Name      Group
----- ----      -----
  180 Running   {AdobeARMservice, Appinfo, ...}
  120 Stopped   {AJRouter, ALG, ...}
```

> **Screenshot 5**
>
> Show: Grouped services
> Expected: Count by status

Services that can be stopped:

```powershell
Get-Service |
    Where-Object { $_.Status -eq "Running" -and $_.CanStop } |
    Select-Object Name, DisplayName, Status
```

> **Screenshot 6**
>
> Show: Stoppable services
> Expected: Filtered by CanStop


## TASK 3: File System Pipeline

Goal: Process files using pipelines.

Get files larger than 1MB:

```powershell
Get-ChildItem -Path C:\Windows\Temp -File |
    Where-Object { $_.Length -gt 1MB } |
    Sort-Object Length -Descending |
    Select-Object Name, @{N='SizeMB';E={[math]::Round($_.Length/1MB,2)}}
```

> **Screenshot 7**
>
> Show: Large files with size
> Expected: Files over 1MB

Get files modified today:

```powershell
Get-ChildItem -Path . -Recurse |
    Where-Object { $_.LastWriteTime -gt (Get-Date).Date }
```

> **Screenshot 8**
>
> Show: Today's modified files
> Expected: Recently modified files

Total size of files:

```powershell
Get-ChildItem -Path . -File -Recurse |
    Measure-Object -Property Length -Sum |
    Select-Object Count, @{N='TotalMB';E={[math]::Round($_.Sum/1MB,2)}}
```

Expected output:

```
Count TotalMB
----- -------
  156  234.56
```

> **Screenshot 9**
>
> Show: File statistics
> Expected: Count and total size


## TASK 4: Pipeline with Calculated Properties

Goal: Create custom properties in pipeline.

Process memory in MB:

```powershell
Get-Process |
    Select-Object Name, Id,
        @{Name='MemoryMB'; Expression={[math]::Round($_.WorkingSet64/1MB,2)}},
        @{Name='CPUSec'; Expression={[math]::Round($_.CPU,2)}} |
    Sort-Object MemoryMB -Descending |
    Select-Object -First 10
```

> **Screenshot 10**
>
> Show: Calculated properties
> Expected: Custom MemoryMB column

Service uptime calculation:

```powershell
Get-Process |
    Where-Object { $_.StartTime } |
    Select-Object Name,
        @{N='StartTime';E={$_.StartTime}},
        @{N='RunningHours';E={[math]::Round(((Get-Date) - $_.StartTime).TotalHours,2)}} |
    Sort-Object RunningHours -Descending |
    Select-Object -First 5
```

> **Screenshot 11**
>
> Show: Process uptime
> Expected: Running hours calculated


## TASK 5: Export Pipeline Results

Goal: Export pipeline output to files.

Export to CSV:

```powershell
Get-Process |
    Select-Object Name, Id, CPU, WorkingSet64 |
    Export-Csv -Path "processes.csv" -NoTypeInformation
```

> **Screenshot 12**
>
> Show: Export to CSV
> Expected: File created

Export to HTML:

```powershell
Get-Service |
    Where-Object { $_.Status -eq "Running" } |
    Select-Object Name, DisplayName, Status |
    ConvertTo-Html -Title "Running Services" |
    Out-File "services.html"
```

> **Screenshot 13**
>
> Show: Export to HTML
> Expected: HTML file created

Pipeline to JSON:

```powershell
Get-Process |
    Select-Object Name, Id, CPU -First 5 |
    ConvertTo-Json |
    Out-File "processes.json"
```

> **Screenshot 14**
>
> Show: Export to JSON
> Expected: JSON file created


## COMPLETE SOLUTION SCRIPT

```powershell
# Task 1: Filter Processes
Write-Host "=== Top Memory Consumers ===" -ForegroundColor Cyan
Get-Process |
    Sort-Object WorkingSet64 -Descending |
    Select-Object Name, @{N='MemMB';E={[math]::Round($_.WorkingSet64/1MB,2)}} -First 5 |
    Format-Table -AutoSize

# Task 2: Service Analysis
Write-Host "`n=== Services by Status ===" -ForegroundColor Cyan
Get-Service | Group-Object Status | Format-Table -AutoSize

# Task 3: File Analysis
Write-Host "`n=== Large Files ===" -ForegroundColor Cyan
Get-ChildItem $env:TEMP -File -ErrorAction SilentlyContinue |
    Where-Object { $_.Length -gt 100KB } |
    Select-Object Name, @{N='KB';E={[math]::Round($_.Length/1KB,2)}} -First 5 |
    Format-Table -AutoSize

# Task 4: Pipeline Export
Write-Host "`n=== Exporting Data ===" -ForegroundColor Cyan
Get-Process |
    Select-Object Name, Id, CPU -First 10 |
    Export-Csv "process_report.csv" -NoTypeInformation
Write-Host "Exported to process_report.csv" -ForegroundColor Green
```

> **Screenshot 15**
>
> Show: Complete pipeline script
> Expected: All tasks executed


## PIPELINE CMDLET REFERENCE

Where-Object:

```powershell
# Full syntax
Where-Object { $_.Property -eq "Value" }

# Simplified syntax
Where-Object Property -eq "Value"

# Multiple conditions
Where-Object { $_.Status -eq "Running" -and $_.Name -like "W*" }
```

Select-Object:

```powershell
# Select properties
Select-Object Name, Id

# First/Last N items
Select-Object -First 10
Select-Object -Last 5

# Calculated property
Select-Object @{Name='Custom';Expression={$_.Value * 2}}

# Expand property (get values, not objects)
Select-Object -ExpandProperty Name
```

Sort-Object:

```powershell
# Ascending (default)
Sort-Object Name

# Descending
Sort-Object CPU -Descending

# Multiple properties
Sort-Object Status, Name

# Unique values
Sort-Object -Unique
```

ForEach-Object:

```powershell
# Process each item
ForEach-Object { $_.Name.ToUpper() }

# Multiple operations
ForEach-Object -Begin { $sum = 0 } -Process { $sum += $_ } -End { $sum }

# Method call shorthand
ForEach-Object Name
```

Group-Object:

```powershell
# Group by property
Group-Object Status

# Count only
Group-Object Status -NoElement

# As hash table
Group-Object Status -AsHashTable
```


## TROUBLESHOOTING

Problem: Pipeline returns no output
Solution: Check filter conditions

```powershell
# Debug: check without filter first
Get-Process | Select-Object -First 1

# Then add filter
Get-Process | Where-Object { $_.CPU -gt 0 }
```

Problem: "Property not found"
Solution: Check property name

```powershell
Get-Process | Get-Member  # See available properties
```

Problem: Slow pipeline performance
Solution: Filter early in pipeline

```powershell
# Good: filter first
Get-ChildItem | Where-Object { $_.Length -gt 1MB } | Select-Object Name

# Bad: select first, then filter
Get-ChildItem | Select-Object * | Where-Object { $_.Length -gt 1MB }
```

Problem: Out of memory with large data
Solution: Process incrementally

```powershell
Get-ChildItem -Recurse | ForEach-Object {
    # Process one at a time
}
```

Problem: Export-Csv adds quotes everywhere
Solution: This is normal CSV behavior for safety

Problem: Pipeline variable $_ not working
Solution: Must be inside script block { }

```powershell
# Wrong:
Get-Process | Where-Object $_.Name -eq "notepad"

# Right:
Get-Process | Where-Object { $_.Name -eq "notepad" }
```

> **Screenshot 16**
>
> Show: Summary of pipeline tasks
> Expected: All pipeline operations working


## SELF-REVIEW CHECKLIST

- [ ] Can filter objects with Where-Object
- [ ] Can select specific properties with Select-Object
- [ ] Can sort results with Sort-Object
- [ ] Can process each item with ForEach-Object
- [ ] Can group items with Group-Object
- [ ] Can calculate statistics with Measure-Object
- [ ] Understands $_ (current pipeline object)
- [ ] Can create calculated properties
- [ ] Can export to CSV, JSON, HTML
- [ ] Knows to filter early for performance
- [ ] Understands pipeline object flow
- [ ] Can chain multiple pipeline operations
