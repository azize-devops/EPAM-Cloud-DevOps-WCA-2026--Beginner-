# MANAGE DATA STRUCTURED FILE FORMATS WITH POWERSHELL - ANSWER

This guide covers exporting and importing data in structured formats:
CSV, XML, JSON, and YAML using PowerShell cmdlets.


## STRUCTURED FILE FORMATS OVERVIEW

Format comparison:

| Format | Human Readable | Data Types | Nested Data | PowerShell Support |
|--------|----------------|------------|-------------|-------------------|
| CSV    | Yes            | Strings    | No          | Native            |
| XML    | Yes            | All types  | Yes         | Native            |
| JSON   | Yes            | Basic types| Yes         | Native            |
| YAML   | Yes            | All types  | Yes         | Module required   |

PowerShell cmdlets:

```powershell
CSV:   Export-Csv, Import-Csv, ConvertTo-Csv, ConvertFrom-Csv
XML:   Export-Clixml, Import-Clixml, ConvertTo-Xml
JSON:  ConvertTo-Json, ConvertFrom-Json
YAML:  ConvertTo-Yaml, ConvertFrom-Yaml (requires powershell-yaml module)
```


## TASK 1: Export Process Data to Multiple Formats

Goal: Get process data and export to CSV, XML, JSON, and YAML files.

Step 1 - Get process data:

```powershell
# Get all Chrome processes (or any other process)
$processes = Get-Process -Name "chrome" -ErrorAction SilentlyContinue

# If Chrome not running, use any available process
if (-not $processes) {
    $processes = Get-Process | Select-Object -First 5
}

# Select required properties
$processData = $processes | Select-Object Name, Id, StartTime, PagedMemorySize
```

> **Screenshot 1**
>
> Show: Getting process data
> Expected: Process objects with selected properties

Complete script1.ps1:

```powershell
param(
    [string]$ProcessName = "chrome",
    [string]$OutputPath = "."
)

Write-Host "Exporting process data to multiple formats..." -ForegroundColor Cyan

# Get processes
$processes = Get-Process -Name $ProcessName -ErrorAction SilentlyContinue

if (-not $processes) {
    Write-Host "Process '$ProcessName' not found. Using first 5 processes." -ForegroundColor Yellow
    $processes = Get-Process | Select-Object -First 5
}

# Select required properties
$processData = $processes | Select-Object Name, Id,
    @{N='StartTime'; E={$_.StartTime.ToString('yyyy-MM-dd HH:mm:ss')}},
    PagedMemorySize

Write-Host "Found $($processData.Count) processes" -ForegroundColor Green

# EXPORT TO CSV
$csvPath = Join-Path $OutputPath "processes.csv"
$processData | Export-Csv -Path $csvPath -NoTypeInformation
Write-Host "CSV exported: $csvPath" -ForegroundColor Green

# EXPORT TO XML
$xmlPath = Join-Path $OutputPath "processes.xml"
$processData | Export-Clixml -Path $xmlPath
Write-Host "XML exported: $xmlPath" -ForegroundColor Green

# EXPORT TO JSON
$jsonPath = Join-Path $OutputPath "processes.json"
$processData | ConvertTo-Json -Depth 3 | Set-Content -Path $jsonPath
Write-Host "JSON exported: $jsonPath" -ForegroundColor Green

# EXPORT TO YAML (requires powershell-yaml module)
$yamlPath = Join-Path $OutputPath "processes.yaml"

# Check if powershell-yaml module is available
if (Get-Module -ListAvailable -Name powershell-yaml) {
    Import-Module powershell-yaml
    $processData | ConvertTo-Yaml | Set-Content -Path $yamlPath
    Write-Host "YAML exported: $yamlPath" -ForegroundColor Green
}
else {
    Write-Host "YAML: powershell-yaml module not installed" -ForegroundColor Yellow
    Write-Host "  Install with: Install-Module -Name powershell-yaml" -ForegroundColor Gray

    # Manual YAML creation as fallback
    $yamlContent = @()
    $yamlContent += "# Process Data"
    $yamlContent += "processes:"
    foreach ($proc in $processData) {
        $yamlContent += "  - name: $($proc.Name)"
        $yamlContent += "    id: $($proc.Id)"
        $yamlContent += "    startTime: `"$($proc.StartTime)`""
        $yamlContent += "    pagedMemorySize: $($proc.PagedMemorySize)"
    }
    $yamlContent | Set-Content -Path $yamlPath
    Write-Host "YAML exported (manual format): $yamlPath" -ForegroundColor Green
}

Write-Host "`nAll exports completed!" -ForegroundColor Cyan
```

> **Screenshot 2**
>
> Show: script1.ps1 content
> Expected: Complete export script

Test the script:

```powershell
.\script1.ps1 -ProcessName "powershell"
# or
.\script1.ps1 -ProcessName "chrome" -OutputPath "C:\Temp"
```

> **Screenshot 3**
>
> Show: Script execution output
> Expected: All four files exported


## TASK 2: Create Hash Tables from Structured Files

Goal: Import data from each file format and create hash tables.

Complete script2.ps1:

```powershell
param(
    [string]$InputPath = "."
)

Write-Host "Creating hash tables from structured files..." -ForegroundColor Cyan
Write-Host "=" * 50

# IMPORT FROM CSV AND CREATE HASH TABLE
Write-Host "`n[CSV] Importing and creating hash table..." -ForegroundColor Yellow

$csvPath = Join-Path $InputPath "processes.csv"

if (Test-Path $csvPath) {
    $csvData = Import-Csv -Path $csvPath

    # Create hash table with Process ID as key
    $csvHashTable = @{}
    foreach ($item in $csvData) {
        $csvHashTable[$item.Id] = @{
            Name = $item.Name
            Id = $item.Id
            StartTime = $item.StartTime
            PagedMemorySize = $item.PagedMemorySize
        }
    }

    Write-Host "CSV Hash Table created with $($csvHashTable.Count) entries"
}
else {
    Write-Host "CSV file not found: $csvPath" -ForegroundColor Red
}

# IMPORT FROM XML AND CREATE HASH TABLE
Write-Host "`n[XML] Importing and creating hash table..." -ForegroundColor Yellow

$xmlPath = Join-Path $InputPath "processes.xml"

if (Test-Path $xmlPath) {
    $xmlData = Import-Clixml -Path $xmlPath

    # Create hash table with Process ID as key
    $xmlHashTable = @{}
    foreach ($item in $xmlData) {
        $xmlHashTable[$item.Id] = @{
            Name = $item.Name
            Id = $item.Id
            StartTime = $item.StartTime
            PagedMemorySize = $item.PagedMemorySize
        }
    }

    Write-Host "XML Hash Table created with $($xmlHashTable.Count) entries"
}
else {
    Write-Host "XML file not found: $xmlPath" -ForegroundColor Red
}

# IMPORT FROM JSON AND CREATE HASH TABLE
Write-Host "`n[JSON] Importing and creating hash table..." -ForegroundColor Yellow

$jsonPath = Join-Path $InputPath "processes.json"

if (Test-Path $jsonPath) {
    $jsonContent = Get-Content -Path $jsonPath -Raw
    $jsonData = $jsonContent | ConvertFrom-Json

    # Create hash table with Process ID as key
    $jsonHashTable = @{}
    foreach ($item in $jsonData) {
        $jsonHashTable[$item.Id] = @{
            Name = $item.Name
            Id = $item.Id
            StartTime = $item.StartTime
            PagedMemorySize = $item.PagedMemorySize
        }
    }

    Write-Host "JSON Hash Table created with $($jsonHashTable.Count) entries"
}
else {
    Write-Host "JSON file not found: $jsonPath" -ForegroundColor Red
}

# SUMMARY
Write-Host "`n" + "=" * 50 -ForegroundColor Cyan
Write-Host "SUMMARY" -ForegroundColor Cyan
Write-Host "=" * 50

Write-Host "CSV Hash Table entries:  $($csvHashTable.Count)"
Write-Host "XML Hash Table entries:  $($xmlHashTable.Count)"
Write-Host "JSON Hash Table entries: $($jsonHashTable.Count)"

# Return hash tables
return @{
    CSV = $csvHashTable
    XML = $xmlHashTable
    JSON = $jsonHashTable
}
```

> **Screenshot 4**
>
> Show: script2.ps1 content
> Expected: Complete import and hash table script

Test the script:

```powershell
$allHashTables = .\script2.ps1
```

> **Screenshot 5**
>
> Show: Script execution output
> Expected: Hash tables created from all formats


## CSV CMDLETS REFERENCE

| Cmdlet          | Description                    | Example                  |
|-----------------|--------------------------------|--------------------------|
| Export-Csv      | Export to CSV file             | $data | Export-Csv f.csv|
| Import-Csv      | Import from CSV file           | Import-Csv f.csv         |
| ConvertTo-Csv   | Convert to CSV string          | $data | ConvertTo-Csv    |
| ConvertFrom-Csv | Convert from CSV string        | $csv | ConvertFrom-Csv   |

Export-Csv parameters:

```powershell
-NoTypeInformation    # Omit type header line
-Delimiter            # Custom delimiter (default comma)
-Encoding             # File encoding
-Append               # Append to existing file
-Force                # Overwrite read-only file
```


## XML CMDLETS REFERENCE

| Cmdlet          | Description                    | Example                  |
|-----------------|--------------------------------|--------------------------|
| Export-Clixml   | Export to CLI XML file         | $data | Export-Clixml f.xml|
| Import-Clixml   | Import from CLI XML file       | Import-Clixml f.xml      |
| ConvertTo-Xml   | Convert to XML object          | $data | ConvertTo-Xml    |
| Select-Xml      | Select nodes with XPath        | Select-Xml -XPath "//node"|

Note: Export-Clixml creates PowerShell-specific XML format.
      For standard XML, use ConvertTo-Xml or .NET XML classes.


## JSON CMDLETS REFERENCE

| Cmdlet           | Description                   | Example                  |
|------------------|-------------------------------|--------------------------|
| ConvertTo-Json   | Convert to JSON string        | $data | ConvertTo-Json   |
| ConvertFrom-Json | Convert from JSON string      | $json | ConvertFrom-Json |

ConvertTo-Json parameters:

```powershell
-Depth           # Nesting depth (default 2)
-Compress        # Remove whitespace
-AsArray         # Force array output
-EnumsAsStrings  # Convert enums to strings
```

Examples:

```powershell
# Export to JSON file
$data | ConvertTo-Json -Depth 5 | Set-Content "data.json"

# Import from JSON file
$data = Get-Content "data.json" -Raw | ConvertFrom-Json

# Compact JSON
$data | ConvertTo-Json -Compress
```


## YAML SUPPORT (POWERSHELL-YAML MODULE)

Installation:

```powershell
Install-Module -Name powershell-yaml -Force
```

Usage:

```powershell
Import-Module powershell-yaml

# Convert to YAML
$data | ConvertTo-Yaml

# Convert from YAML
$yamlString | ConvertFrom-Yaml

# Export to YAML file
$data | ConvertTo-Yaml | Set-Content "data.yaml"

# Import from YAML file
Get-Content "data.yaml" -Raw | ConvertFrom-Yaml
```


## HASH TABLE CREATION PATTERNS

Pattern 1 - ForEach loop:

```powershell
$hashTable = @{}
foreach ($item in $data) {
    $hashTable[$item.Id] = $item
}
```

Pattern 2 - ForEach-Object pipeline:

```powershell
$hashTable = @{}
$data | ForEach-Object { $hashTable[$_.Id] = $_ }
```

Pattern 3 - Group-Object (unique keys):

```powershell
$hashTable = $data | Group-Object -Property Id -AsHashTable
```


## TROUBLESHOOTING

Problem: Export-Csv adds #TYPE header
Solution: Use -NoTypeInformation parameter

```powershell
$data | Export-Csv -Path file.csv -NoTypeInformation
```

Problem: JSON depth too shallow
Solution: Increase -Depth parameter

```powershell
$data | ConvertTo-Json -Depth 10
```

Problem: Import-Csv returns strings, not typed data
Solution: Convert types manually or use calculated properties

```powershell
Import-Csv file.csv | ForEach-Object {
    $_.Id = [int]$_.Id
    $_
}
```

Problem: YAML module not found
Solution: Install powershell-yaml module

```powershell
Install-Module -Name powershell-yaml -Scope CurrentUser
```

Problem: Hash table key collision
Solution: Use unique identifier or handle duplicates

```powershell
if (-not $hashTable.ContainsKey($key)) {
    $hashTable[$key] = $value
}
```

> **Screenshot 6**
>
> Show: Summary of data format tasks
> Expected: Both tasks completed successfully


## SELF-REVIEW CHECKLIST

- [ ] Task 1: Get-Process retrieves process data
- [ ] Task 1: Select-Object picks Name, Id, StartTime, PagedMemorySize
- [ ] Task 1: Export-Csv creates processes.csv
- [ ] Task 1: Export-Clixml creates processes.xml
- [ ] Task 1: ConvertTo-Json creates processes.json
- [ ] Task 1: YAML file created (with or without module)
- [ ] Task 2: Import-Csv reads CSV file
- [ ] Task 2: Import-Clixml reads XML file
- [ ] Task 2: ConvertFrom-Json reads JSON file
- [ ] Task 2: Hash table created with process ID as key
- [ ] Task 2: Each format produces same data structure
- [ ] Understand CSV, XML, JSON format differences
- [ ] Know how to install powershell-yaml module
- [ ] Understand hash table creation patterns
- [ ] Know Export-Csv -NoTypeInformation usage
