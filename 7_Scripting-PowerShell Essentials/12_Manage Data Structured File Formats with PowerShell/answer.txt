MANAGE DATA STRUCTURED FILE FORMATS WITH POWERSHELL - ANSWER
=============================================================

This guide covers exporting and importing data in structured formats:
CSV, XML, JSON, and YAML using PowerShell cmdlets.


================================================================================
                     STRUCTURED FILE FORMATS OVERVIEW
================================================================================

Format comparison:
| Format | Human Readable | Data Types | Nested Data | PowerShell Support |
|--------|----------------|------------|-------------|-------------------|
| CSV    | Yes            | Strings    | No          | Native            |
| XML    | Yes            | All types  | Yes         | Native            |
| JSON   | Yes            | Basic types| Yes         | Native            |
| YAML   | Yes            | All types  | Yes         | Module required   |

PowerShell cmdlets:
    CSV:   Export-Csv, Import-Csv, ConvertTo-Csv, ConvertFrom-Csv
    XML:   Export-Clixml, Import-Clixml, ConvertTo-Xml
    JSON:  ConvertTo-Json, ConvertFrom-Json
    YAML:  ConvertTo-Yaml, ConvertFrom-Yaml (requires powershell-yaml module)

================================================================================


TASK 1: Export Process Data to Multiple Formats
-------------------------------------------------

Goal: Get process data and export to CSV, XML, JSON, and YAML files.

Step 1 - Get process data:

    # Get all Chrome processes (or any other process)
    $processes = Get-Process -Name "chrome" -ErrorAction SilentlyContinue

    # If Chrome not running, use any available process
    if (-not $processes) {
        $processes = Get-Process | Select-Object -First 5
    }

    # Select required properties
    $processData = $processes | Select-Object Name, Id, StartTime, PagedMemorySize

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Getting process data                              |
|  Expected: Process objects with selected properties      |
+----------------------------------------------------------+

Complete script1.ps1:

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

    # ============================================
    # EXPORT TO CSV
    # ============================================
    $csvPath = Join-Path $OutputPath "processes.csv"
    $processData | Export-Csv -Path $csvPath -NoTypeInformation
    Write-Host "CSV exported: $csvPath" -ForegroundColor Green

    # ============================================
    # EXPORT TO XML
    # ============================================
    $xmlPath = Join-Path $OutputPath "processes.xml"
    $processData | Export-Clixml -Path $xmlPath
    Write-Host "XML exported: $xmlPath" -ForegroundColor Green

    # ============================================
    # EXPORT TO JSON
    # ============================================
    $jsonPath = Join-Path $OutputPath "processes.json"
    $processData | ConvertTo-Json -Depth 3 | Set-Content -Path $jsonPath
    Write-Host "JSON exported: $jsonPath" -ForegroundColor Green

    # ============================================
    # EXPORT TO YAML (requires powershell-yaml module)
    # ============================================
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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: script1.ps1 content                               |
|  Expected: Complete export script                        |
+----------------------------------------------------------+

Test the script:

    .\script1.ps1 -ProcessName "powershell"
    # or
    .\script1.ps1 -ProcessName "chrome" -OutputPath "C:\Temp"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Script execution output                           |
|  Expected: All four files exported                       |
+----------------------------------------------------------+

View CSV file content:

    Get-Content .\processes.csv

Expected output:

    "Name","Id","StartTime","PagedMemorySize"
    "chrome","12345","2024-01-15 10:30:45","125829120"
    "chrome","12346","2024-01-15 10:30:46","98304000"
    "chrome","12347","2024-01-15 10:30:47","67108864"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: CSV file content                                  |
|  Expected: Comma-separated values                        |
+----------------------------------------------------------+

View XML file content:

    Get-Content .\processes.xml

Expected output (partial):

    <Objs Version="1.1.0.1" xmlns="...">
      <Obj RefId="0">
        <TN RefId="0">
          <T>Selected.System.Diagnostics.Process</T>
        </TN>
        <MS>
          <S N="Name">chrome</S>
          <I32 N="Id">12345</I32>
          <S N="StartTime">2024-01-15 10:30:45</S>
          <I64 N="PagedMemorySize">125829120</I64>
        </MS>
      </Obj>
    </Objs>

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: XML file content                                  |
|  Expected: XML structured data                           |
+----------------------------------------------------------+

View JSON file content:

    Get-Content .\processes.json

Expected output:

    [
      {
        "Name": "chrome",
        "Id": 12345,
        "StartTime": "2024-01-15 10:30:45",
        "PagedMemorySize": 125829120
      },
      {
        "Name": "chrome",
        "Id": 12346,
        "StartTime": "2024-01-15 10:30:46",
        "PagedMemorySize": 98304000
      }
    ]

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: JSON file content                                 |
|  Expected: JSON array of objects                         |
+----------------------------------------------------------+

View YAML file content:

    Get-Content .\processes.yaml

Expected output:

    # Process Data
    processes:
      - name: chrome
        id: 12345
        startTime: "2024-01-15 10:30:45"
        pagedMemorySize: 125829120
      - name: chrome
        id: 12346
        startTime: "2024-01-15 10:30:46"
        pagedMemorySize: 98304000

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: YAML file content                                 |
|  Expected: YAML formatted data                           |
+----------------------------------------------------------+


TASK 2: Create Hash Tables from Structured Files
--------------------------------------------------

Goal: Import data from each file format and create hash tables.

Complete script2.ps1:

    param(
        [string]$InputPath = "."
    )

    Write-Host "Creating hash tables from structured files..." -ForegroundColor Cyan
    Write-Host "=" * 50

    # ============================================
    # IMPORT FROM CSV AND CREATE HASH TABLE
    # ============================================
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
        Write-Host "Sample entry:"
        $sampleKey = $csvHashTable.Keys | Select-Object -First 1
        $csvHashTable[$sampleKey] | Format-Table
    }
    else {
        Write-Host "CSV file not found: $csvPath" -ForegroundColor Red
    }

    # ============================================
    # IMPORT FROM XML AND CREATE HASH TABLE
    # ============================================
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
        Write-Host "Sample entry:"
        $sampleKey = $xmlHashTable.Keys | Select-Object -First 1
        $xmlHashTable[$sampleKey] | Format-Table
    }
    else {
        Write-Host "XML file not found: $xmlPath" -ForegroundColor Red
    }

    # ============================================
    # IMPORT FROM JSON AND CREATE HASH TABLE
    # ============================================
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
        Write-Host "Sample entry:"
        $sampleKey = $jsonHashTable.Keys | Select-Object -First 1
        $jsonHashTable[$sampleKey] | Format-Table
    }
    else {
        Write-Host "JSON file not found: $jsonPath" -ForegroundColor Red
    }

    # ============================================
    # IMPORT FROM YAML AND CREATE HASH TABLE
    # ============================================
    Write-Host "`n[YAML] Importing and creating hash table..." -ForegroundColor Yellow

    $yamlPath = Join-Path $InputPath "processes.yaml"

    if (Test-Path $yamlPath) {
        if (Get-Module -ListAvailable -Name powershell-yaml) {
            Import-Module powershell-yaml
            $yamlContent = Get-Content -Path $yamlPath -Raw
            $yamlData = $yamlContent | ConvertFrom-Yaml

            # Create hash table
            $yamlHashTable = @{}
            $processes = if ($yamlData.processes) { $yamlData.processes } else { $yamlData }

            foreach ($item in $processes) {
                $id = if ($item.id) { $item.id } else { $item.Id }
                $yamlHashTable[$id] = @{
                    Name = if ($item.name) { $item.name } else { $item.Name }
                    Id = $id
                    StartTime = if ($item.startTime) { $item.startTime } else { $item.StartTime }
                    PagedMemorySize = if ($item.pagedMemorySize) { $item.pagedMemorySize } else { $item.PagedMemorySize }
                }
            }

            Write-Host "YAML Hash Table created with $($yamlHashTable.Count) entries"
            Write-Host "Sample entry:"
            $sampleKey = $yamlHashTable.Keys | Select-Object -First 1
            $yamlHashTable[$sampleKey] | Format-Table
        }
        else {
            Write-Host "powershell-yaml module not installed" -ForegroundColor Yellow

            # Manual YAML parsing fallback
            $yamlHashTable = @{}
            $yamlContent = Get-Content -Path $yamlPath
            $currentItem = @{}
            $currentId = $null

            foreach ($line in $yamlContent) {
                if ($line -match '^\s+-\s+name:\s+(.+)$') {
                    if ($currentId) {
                        $yamlHashTable[$currentId] = $currentItem
                    }
                    $currentItem = @{ Name = $Matches[1] }
                }
                elseif ($line -match '^\s+id:\s+(\d+)$') {
                    $currentId = $Matches[1]
                    $currentItem['Id'] = $currentId
                }
                elseif ($line -match '^\s+startTime:\s+"?(.+?)"?$') {
                    $currentItem['StartTime'] = $Matches[1]
                }
                elseif ($line -match '^\s+pagedMemorySize:\s+(\d+)$') {
                    $currentItem['PagedMemorySize'] = $Matches[1]
                }
            }
            if ($currentId) {
                $yamlHashTable[$currentId] = $currentItem
            }

            Write-Host "YAML Hash Table created (manual parse) with $($yamlHashTable.Count) entries"
        }
    }
    else {
        Write-Host "YAML file not found: $yamlPath" -ForegroundColor Red
    }

    # ============================================
    # SUMMARY
    # ============================================
    Write-Host "`n" + "=" * 50 -ForegroundColor Cyan
    Write-Host "SUMMARY" -ForegroundColor Cyan
    Write-Host "=" * 50

    Write-Host "CSV Hash Table entries:  $($csvHashTable.Count)"
    Write-Host "XML Hash Table entries:  $($xmlHashTable.Count)"
    Write-Host "JSON Hash Table entries: $($jsonHashTable.Count)"
    Write-Host "YAML Hash Table entries: $($yamlHashTable.Count)"

    # Return hash tables
    return @{
        CSV = $csvHashTable
        XML = $xmlHashTable
        JSON = $jsonHashTable
        YAML = $yamlHashTable
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: script2.ps1 content                               |
|  Expected: Complete import and hash table script         |
+----------------------------------------------------------+

Test the script:

    $allHashTables = .\script2.ps1

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Script execution output                           |
|  Expected: Hash tables created from all formats          |
+----------------------------------------------------------+

Expected output:

    Creating hash tables from structured files...
    ==================================================

    [CSV] Importing and creating hash table...
    CSV Hash Table created with 3 entries
    Sample entry:

    Name                           Value
    ----                           -----
    Name                           chrome
    Id                             12345
    StartTime                      2024-01-15 10:30:45
    PagedMemorySize                125829120

    [XML] Importing and creating hash table...
    XML Hash Table created with 3 entries
    ...

    [JSON] Importing and creating hash table...
    JSON Hash Table created with 3 entries
    ...

    ==================================================
    SUMMARY
    ==================================================
    CSV Hash Table entries:  3
    XML Hash Table entries:  3
    JSON Hash Table entries: 3
    YAML Hash Table entries: 3

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Script output with hash table samples             |
|  Expected: Sample entries displayed                      |
+----------------------------------------------------------+

Working with the returned hash tables:

    # Get all hash tables
    $allHashTables = .\script2.ps1

    # Access CSV hash table
    $allHashTables.CSV

    # Get specific process by ID
    $allHashTables.JSON["12345"]

    # List all process names from XML
    $allHashTables.XML.Values | ForEach-Object { $_.Name }

    # Compare entries across formats
    $csvEntry = $allHashTables.CSV.Values | Select-Object -First 1
    $jsonEntry = $allHashTables.JSON.Values | Select-Object -First 1

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Working with returned hash tables                 |
|  Expected: Accessing data from different formats         |
+----------------------------------------------------------+


ALTERNATIVE APPROACH - USING PIPELINES
---------------------------------------

One-liner versions:

    # CSV to Hash Table
    $csvHash = @{}
    Import-Csv "processes.csv" | ForEach-Object { $csvHash[$_.Id] = $_ }

    # JSON to Hash Table
    $jsonHash = @{}
    (Get-Content "processes.json" | ConvertFrom-Json) | ForEach-Object { $jsonHash[$_.Id] = $_ }

    # XML to Hash Table
    $xmlHash = @{}
    Import-Clixml "processes.xml" | ForEach-Object { $xmlHash[$_.Id] = $_ }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: One-liner hash table creation                     |
|  Expected: Compact pipeline solutions                    |
+----------------------------------------------------------+


COMPLETE SCRIPT FILES
---------------------

script1.ps1 (Export to All Formats):

    param(
        [string]$ProcessName = "chrome",
        [string]$OutputPath = "."
    )

    # Get processes
    $processes = Get-Process -Name $ProcessName -ErrorAction SilentlyContinue
    if (-not $processes) {
        $processes = Get-Process | Select-Object -First 5
    }

    $processData = $processes | Select-Object Name, Id,
        @{N='StartTime'; E={$_.StartTime.ToString('yyyy-MM-dd HH:mm:ss')}},
        PagedMemorySize

    # Export to CSV
    $processData | Export-Csv -Path (Join-Path $OutputPath "processes.csv") -NoTypeInformation

    # Export to XML
    $processData | Export-Clixml -Path (Join-Path $OutputPath "processes.xml")

    # Export to JSON
    $processData | ConvertTo-Json | Set-Content -Path (Join-Path $OutputPath "processes.json")

    # Export to YAML
    $yamlContent = "processes:`n"
    foreach ($p in $processData) {
        $yamlContent += "  - name: $($p.Name)`n"
        $yamlContent += "    id: $($p.Id)`n"
        $yamlContent += "    startTime: `"$($p.StartTime)`"`n"
        $yamlContent += "    pagedMemorySize: $($p.PagedMemorySize)`n"
    }
    $yamlContent | Set-Content -Path (Join-Path $OutputPath "processes.yaml")

    Write-Host "Exported to CSV, XML, JSON, and YAML" -ForegroundColor Green

script2.ps1 (Import and Create Hash Tables):

    param([string]$InputPath = ".")

    $result = @{}

    # CSV
    $csvHash = @{}
    Import-Csv (Join-Path $InputPath "processes.csv") |
        ForEach-Object { $csvHash[$_.Id] = $_ }
    $result['CSV'] = $csvHash

    # XML
    $xmlHash = @{}
    Import-Clixml (Join-Path $InputPath "processes.xml") |
        ForEach-Object { $xmlHash[$_.Id] = $_ }
    $result['XML'] = $xmlHash

    # JSON
    $jsonHash = @{}
    Get-Content (Join-Path $InputPath "processes.json") -Raw |
        ConvertFrom-Json |
        ForEach-Object { $jsonHash[$_.Id] = $_ }
    $result['JSON'] = $jsonHash

    # Display summary
    Write-Host "Hash tables created:" -ForegroundColor Cyan
    $result.Keys | ForEach-Object {
        Write-Host "  $_: $($result[$_].Count) entries"
    }

    return $result

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
                     CSV CMDLETS REFERENCE
================================================================================

| Cmdlet          | Description                    | Example                  |
|-----------------|--------------------------------|--------------------------|
| Export-Csv      | Export to CSV file             | $data | Export-Csv f.csv|
| Import-Csv      | Import from CSV file           | Import-Csv f.csv         |
| ConvertTo-Csv   | Convert to CSV string          | $data | ConvertTo-Csv    |
| ConvertFrom-Csv | Convert from CSV string        | $csv | ConvertFrom-Csv   |

Export-Csv parameters:
    -NoTypeInformation    Omit type header line
    -Delimiter            Custom delimiter (default comma)
    -Encoding             File encoding
    -Append               Append to existing file
    -Force                Overwrite read-only file

================================================================================


================================================================================
                     XML CMDLETS REFERENCE
================================================================================

| Cmdlet          | Description                    | Example                  |
|-----------------|--------------------------------|--------------------------|
| Export-Clixml   | Export to CLI XML file         | $data | Export-Clixml f.xml|
| Import-Clixml   | Import from CLI XML file       | Import-Clixml f.xml      |
| ConvertTo-Xml   | Convert to XML object          | $data | ConvertTo-Xml    |
| Select-Xml      | Select nodes with XPath        | Select-Xml -XPath "//node"|

Note: Export-Clixml creates PowerShell-specific XML format.
      For standard XML, use ConvertTo-Xml or .NET XML classes.

================================================================================


================================================================================
                     JSON CMDLETS REFERENCE
================================================================================

| Cmdlet           | Description                   | Example                  |
|------------------|-------------------------------|--------------------------|
| ConvertTo-Json   | Convert to JSON string        | $data | ConvertTo-Json   |
| ConvertFrom-Json | Convert from JSON string      | $json | ConvertFrom-Json |

ConvertTo-Json parameters:
    -Depth           Nesting depth (default 2)
    -Compress        Remove whitespace
    -AsArray         Force array output
    -EnumsAsStrings  Convert enums to strings

Examples:

    # Export to JSON file
    $data | ConvertTo-Json -Depth 5 | Set-Content "data.json"

    # Import from JSON file
    $data = Get-Content "data.json" -Raw | ConvertFrom-Json

    # Compact JSON
    $data | ConvertTo-Json -Compress

================================================================================


================================================================================
                     YAML SUPPORT (POWERSHELL-YAML MODULE)
================================================================================

Installation:

    Install-Module -Name powershell-yaml -Force

Usage:

    Import-Module powershell-yaml

    # Convert to YAML
    $data | ConvertTo-Yaml

    # Convert from YAML
    $yamlString | ConvertFrom-Yaml

    # Export to YAML file
    $data | ConvertTo-Yaml | Set-Content "data.yaml"

    # Import from YAML file
    Get-Content "data.yaml" -Raw | ConvertFrom-Yaml

================================================================================


================================================================================
                     HASH TABLE CREATION PATTERNS
================================================================================

Pattern 1 - ForEach loop:

    $hashTable = @{}
    foreach ($item in $data) {
        $hashTable[$item.Id] = $item
    }

Pattern 2 - ForEach-Object pipeline:

    $hashTable = @{}
    $data | ForEach-Object { $hashTable[$_.Id] = $_ }

Pattern 3 - Group-Object (unique keys):

    $hashTable = $data | Group-Object -Property Id -AsHashTable

Pattern 4 - Nested hash table:

    $hashTable = @{}
    foreach ($item in $data) {
        $hashTable[$item.Id] = @{
            Name = $item.Name
            Value = $item.Value
        }
    }

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: Export-Csv adds #TYPE header
Solution: Use -NoTypeInformation parameter
    $data | Export-Csv -Path file.csv -NoTypeInformation

Problem: JSON depth too shallow
Solution: Increase -Depth parameter
    $data | ConvertTo-Json -Depth 10

Problem: Import-Csv returns strings, not typed data
Solution: Convert types manually or use calculated properties
    Import-Csv file.csv | ForEach-Object {
        $_.Id = [int]$_.Id
        $_
    }

Problem: XML special characters cause errors
Solution: Use Export-Clixml or escape characters
    [System.Security.SecurityElement]::Escape($text)

Problem: YAML module not found
Solution: Install powershell-yaml module
    Install-Module -Name powershell-yaml -Scope CurrentUser

Problem: Hash table key collision
Solution: Use unique identifier or handle duplicates
    if (-not $hashTable.ContainsKey($key)) {
        $hashTable[$key] = $value
    }

Problem: StartTime is null for some processes
Solution: Handle null values
    @{N='StartTime'; E={
        if ($_.StartTime) { $_.StartTime.ToString('yyyy-MM-dd HH:mm:ss') }
        else { 'N/A' }
    }}

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Summary of data format tasks                      |
|  Expected: Both tasks completed successfully             |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Task 1: Get-Process retrieves process data
[ ] Task 1: Select-Object picks Name, Id, StartTime, PagedMemorySize
[ ] Task 1: Export-Csv creates processes.csv
[ ] Task 1: Export-Clixml creates processes.xml
[ ] Task 1: ConvertTo-Json creates processes.json
[ ] Task 1: YAML file created (with or without module)
[ ] Task 2: Import-Csv reads CSV file
[ ] Task 2: Import-Clixml reads XML file
[ ] Task 2: ConvertFrom-Json reads JSON file
[ ] Task 2: Hash table created with process ID as key
[ ] Task 2: Each format produces same data structure
[ ] Understand CSV, XML, JSON format differences
[ ] Know how to install powershell-yaml module
[ ] Understand hash table creation patterns
[ ] Know Export-Csv -NoTypeInformation usage

================================================================================
