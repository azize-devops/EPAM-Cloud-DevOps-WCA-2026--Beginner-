PIPELINES - ANSWER
==================

This guide covers PowerShell pipelines, data streaming between cmdlets,
and advanced pipeline techniques for data processing.


================================================================================
                     PIPELINE BASICS
================================================================================

Pipeline concept:
    Command1 | Command2 | Command3

Data flows from left to right, each command processes output of previous.

Key pipeline cmdlets:
    Where-Object     Filter objects by condition
    ForEach-Object   Process each object
    Select-Object    Select specific properties
    Sort-Object      Sort objects
    Group-Object     Group objects by property
    Measure-Object   Calculate statistics

================================================================================


TASK 1: Associate Running Services with Processes
--------------------------------------------------

Goal: Create hash table linking service names to their process information.

Basic approach - Get running services and their process IDs:

    Get-Service | Where-Object { $_.Status -eq 'Running' }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: List of running services                          |
|  Expected: Services with Running status                  |
+----------------------------------------------------------+

Solution using pipelines:

    # Create hash table using pipeline
    $serviceProcessMap = @{}

    Get-Service |
        Where-Object { $_.Status -eq 'Running' } |
        ForEach-Object {
            $service = $_
            $processId = (Get-CimInstance Win32_Service |
                Where-Object { $_.Name -eq $service.Name }).ProcessId

            if ($processId -and $processId -ne 0) {
                $process = Get-Process -Id $processId -ErrorAction SilentlyContinue
                $serviceProcessMap[$service.Name] = @{
                    ServiceName = $service.Name
                    DisplayName = $service.DisplayName
                    ProcessId = $processId
                    ProcessName = $process.ProcessName
                    CPU = $process.CPU
                    Memory = $process.WorkingSet64
                }
            }
        }

    $serviceProcessMap

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Script creating service-process association       |
|  Expected: Pipeline with ForEach-Object                  |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Hash table output                                 |
|  Expected: Service names as keys with process info       |
+----------------------------------------------------------+

Simplified one-liner approach:

    $result = Get-CimInstance Win32_Service |
        Where-Object { $_.State -eq 'Running' -and $_.ProcessId -ne 0 } |
        ForEach-Object {
            $proc = Get-Process -Id $_.ProcessId -ErrorAction SilentlyContinue
            [PSCustomObject]@{
                ServiceName = $_.Name
                ProcessId = $_.ProcessId
                ProcessName = $proc.ProcessName
                Memory_MB = [math]::Round($proc.WorkingSet64 / 1MB, 2)
            }
        }

    # Convert to hash table
    $hashTable = @{}
    $result | ForEach-Object { $hashTable[$_.ServiceName] = $_ }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Simplified pipeline approach                      |
|  Expected: Clean one-liner with PSCustomObject           |
+----------------------------------------------------------+

Display hash table contents:

    # View all entries
    $hashTable.GetEnumerator() | Sort-Object Name

    # Access specific service
    $hashTable['wuauserv']    # Windows Update service

    # Get all process names
    $hashTable.Values | Select-Object ProcessName -Unique

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Accessing hash table data                         |
|  Expected: Various ways to query the hash table          |
+----------------------------------------------------------+

Complete script (script1.ps1):

    # Associate running services with their processes using pipelines
    # Store result in hash table

    $serviceProcessHash = @{}

    Get-CimInstance Win32_Service |
        Where-Object { $_.State -eq 'Running' } |
        Where-Object { $_.ProcessId -ne 0 } |
        ForEach-Object {
            $serviceName = $_.Name
            $processId = $_.ProcessId

            # Get process details via pipeline
            $processInfo = Get-Process -Id $processId -ErrorAction SilentlyContinue |
                Select-Object ProcessName, Id, CPU,
                    @{N='Memory_MB'; E={[math]::Round($_.WorkingSet64/1MB, 2)}}

            $serviceProcessHash[$serviceName] = $processInfo
        }

    # Display results
    Write-Host "`nService to Process Associations:" -ForegroundColor Green
    Write-Host "=================================" -ForegroundColor Green

    $serviceProcessHash.GetEnumerator() |
        Sort-Object Name |
        ForEach-Object {
            Write-Host "`n$($_.Key):" -ForegroundColor Yellow
            $_.Value | Format-Table -AutoSize
        }

    # Return hash table
    $serviceProcessHash

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Complete script1.ps1                              |
|  Expected: Full pipeline-based solution                  |
+----------------------------------------------------------+

Expected output sample:

    Service to Process Associations:
    =================================

    wuauserv:
    ProcessName    Id  CPU Memory_MB
    -----------    --  --- ---------
    svchost      1234 2.5     45.32

    Spooler:
    ProcessName    Id  CPU Memory_MB
    -----------    --  --- ---------
    spoolsv      5678 0.8     12.15

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Script execution output                           |
|  Expected: Formatted service-process associations        |
+----------------------------------------------------------+

Alternative - Group services by process:

    Get-CimInstance Win32_Service |
        Where-Object { $_.State -eq 'Running' -and $_.ProcessId -ne 0 } |
        Group-Object ProcessId |
        ForEach-Object {
            $proc = Get-Process -Id $_.Name -ErrorAction SilentlyContinue
            [PSCustomObject]@{
                ProcessName = $proc.ProcessName
                ProcessId = $_.Name
                ServiceCount = $_.Count
                Services = ($_.Group | Select-Object -ExpandProperty Name) -join ', '
            }
        } |
        Sort-Object ServiceCount -Descending

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Group by process alternative                      |
|  Expected: Processes with multiple services grouped      |
+----------------------------------------------------------+


TASK 2: Find 10 Most Common Words in Wikipedia Article
-------------------------------------------------------

Goal: Fetch Wikipedia article and find top 10 most frequent words.

Step 1 - Fetch Wikipedia content:

    # Using Invoke-WebRequest
    $url = "https://en.wikipedia.org/wiki/PowerShell"
    $response = Invoke-WebRequest -Uri $url

    # Get text content
    $text = $response.ParsedHtml.body.innerText

    # Alternative for PowerShell Core
    $text = (Invoke-WebRequest -Uri $url).Content

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Fetching Wikipedia article                        |
|  Expected: Web request to Wikipedia                      |
+----------------------------------------------------------+

Step 2 - Process text with pipeline:

    # Split into words, clean, count
    $text -split '\W+' |
        Where-Object { $_.Length -gt 3 } |
        ForEach-Object { $_.ToLower() } |
        Group-Object |
        Sort-Object Count -Descending |
        Select-Object -First 10 Name, Count

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Text processing pipeline                          |
|  Expected: Split, filter, group, sort, select            |
+----------------------------------------------------------+

Complete solution (script2.ps1):

    # Find 10 most common words in Wikipedia article using pipelines

    param(
        [string]$WikiArticle = "PowerShell"
    )

    $url = "https://en.wikipedia.org/wiki/$WikiArticle"

    Write-Host "Fetching article: $url" -ForegroundColor Cyan

    # Fetch and process using pipeline
    $topWords = (Invoke-WebRequest -Uri $url -UseBasicParsing).Content |
        ForEach-Object {
            # Remove HTML tags
            $_ -replace '<[^>]+>', ' '
        } |
        ForEach-Object {
            # Remove special characters and split into words
            $_ -split '\W+'
        } |
        Where-Object {
            # Filter: length > 3, only letters
            $_.Length -gt 3 -and $_ -match '^[a-zA-Z]+$'
        } |
        ForEach-Object {
            # Convert to lowercase
            $_.ToLower()
        } |
        Group-Object |
        Sort-Object Count -Descending |
        Select-Object -First 10 @{N='Word';E={$_.Name}}, Count

    # Display results
    Write-Host "`nTop 10 Most Common Words:" -ForegroundColor Green
    Write-Host "=========================" -ForegroundColor Green
    $topWords | Format-Table -AutoSize

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Complete script2.ps1                              |
|  Expected: Full Wikipedia word count solution            |
+----------------------------------------------------------+

Test the script:

    .\script2.ps1
    # or
    .\script2.ps1 -WikiArticle "Python_(programming_language)"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Script execution                                  |
|  Expected: Top 10 words displayed                        |
+----------------------------------------------------------+

Expected output sample:

    Fetching article: https://en.wikipedia.org/wiki/PowerShell

    Top 10 Most Common Words:
    =========================

    Word        Count
    ----        -----
    powershell     85
    windows        42
    command        38
    that           35
    with           32
    from           28
    script         27
    this           25
    microsoft      24
    cmdlets        22

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Word count results                                |
|  Expected: Words sorted by frequency                     |
+----------------------------------------------------------+

Alternative - Exclude common stop words:

    $stopWords = @('that', 'this', 'with', 'from', 'have', 'been',
                   'were', 'they', 'their', 'which', 'would', 'there',
                   'could', 'other', 'into', 'more', 'some', 'such',
                   'than', 'only', 'also', 'about', 'when', 'what')

    $topWords = (Invoke-WebRequest -Uri $url -UseBasicParsing).Content |
        ForEach-Object { $_ -replace '<[^>]+>', ' ' } |
        ForEach-Object { $_ -split '\W+' } |
        Where-Object { $_.Length -gt 3 -and $_ -match '^[a-zA-Z]+$' } |
        ForEach-Object { $_.ToLower() } |
        Where-Object { $_ -notin $stopWords } |
        Group-Object |
        Sort-Object Count -Descending |
        Select-Object -First 10 @{N='Word';E={$_.Name}}, Count

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Version with stop words filter                    |
|  Expected: More meaningful words in results              |
+----------------------------------------------------------+

Alternative - Using local text file:

    # If web access is not available, use local file
    $text = Get-Content "article.txt" -Raw

    $topWords = $text -split '\W+' |
        Where-Object { $_.Length -gt 3 -and $_ -match '^[a-zA-Z]+$' } |
        ForEach-Object { $_.ToLower() } |
        Group-Object |
        Sort-Object Count -Descending |
        Select-Object -First 10 Name, Count

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Local file alternative                            |
|  Expected: Same pipeline with file input                 |
+----------------------------------------------------------+

Enhanced version with visualization:

    $topWords = (Invoke-WebRequest -Uri $url -UseBasicParsing).Content |
        ForEach-Object { $_ -replace '<[^>]+>', ' ' } |
        ForEach-Object { $_ -split '\W+' } |
        Where-Object { $_.Length -gt 3 -and $_ -match '^[a-zA-Z]+$' } |
        ForEach-Object { $_.ToLower() } |
        Group-Object |
        Sort-Object Count -Descending |
        Select-Object -First 10

    # Visual bar chart
    $maxCount = ($topWords | Measure-Object Count -Maximum).Maximum

    $topWords | ForEach-Object {
        $bar = '#' * [math]::Round(($_.Count / $maxCount) * 40)
        "{0,-15} {1,4} {2}" -f $_.Name, $_.Count, $bar
    }

Output:

    powershell       85 ########################################
    windows          42 ####################
    command          38 ##################
    that             35 ################
    ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: Visual bar chart output                           |
|  Expected: ASCII bar chart of word frequencies           |
+----------------------------------------------------------+


COMPLETE SCRIPT FILES
---------------------

script1.ps1 (Service-Process Association):

    # Associate running services with processes using pipelines
    $serviceProcessHash = @{}

    Get-CimInstance Win32_Service |
        Where-Object { $_.State -eq 'Running' -and $_.ProcessId -ne 0 } |
        ForEach-Object {
            $serviceName = $_.Name
            $processId = $_.ProcessId

            $processInfo = Get-Process -Id $processId -ErrorAction SilentlyContinue |
                Select-Object ProcessName, Id, CPU,
                    @{N='Memory_MB'; E={[math]::Round($_.WorkingSet64/1MB, 2)}}

            $serviceProcessHash[$serviceName] = $processInfo
        }

    # Display
    $serviceProcessHash.GetEnumerator() |
        Sort-Object Name |
        Select-Object -First 20 |
        ForEach-Object {
            [PSCustomObject]@{
                Service = $_.Key
                Process = $_.Value.ProcessName
                PID = $_.Value.Id
                Memory_MB = $_.Value.Memory_MB
            }
        } | Format-Table -AutoSize

script2.ps1 (Word Frequency):

    param([string]$WikiArticle = "PowerShell")

    $url = "https://en.wikipedia.org/wiki/$WikiArticle"

    Write-Host "Analyzing: $url`n" -ForegroundColor Cyan

    (Invoke-WebRequest -Uri $url -UseBasicParsing).Content |
        ForEach-Object { $_ -replace '<[^>]+>', ' ' } |
        ForEach-Object { $_ -split '\W+' } |
        Where-Object { $_.Length -gt 3 -and $_ -match '^[a-zA-Z]+$' } |
        ForEach-Object { $_.ToLower() } |
        Group-Object |
        Sort-Object Count -Descending |
        Select-Object -First 10 @{N='Word';E={$_.Name}}, Count |
        Format-Table -AutoSize

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: Both complete scripts                             |
|  Expected: script1.ps1 and script2.ps1                   |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: Both scripts tested                               |
|  Expected: Successful execution of both tasks            |
+----------------------------------------------------------+


================================================================================
                     PIPELINE CMDLETS REFERENCE
================================================================================

| Cmdlet           | Description                    | Example                   |
|------------------|--------------------------------|---------------------------|
| Where-Object     | Filter objects                 | | Where-Object {$_.X -gt 5}|
| ForEach-Object   | Process each object            | | ForEach-Object {$_.Name} |
| Select-Object    | Select properties              | | Select-Object Name, Id   |
| Sort-Object      | Sort objects                   | | Sort-Object Name         |
| Group-Object     | Group by property              | | Group-Object Status      |
| Measure-Object   | Calculate stats                | | Measure-Object -Sum      |
| Compare-Object   | Compare two sets               | Compare-Object $a $b     |
| Tee-Object       | Split pipeline                 | | Tee-Object -Variable x   |
| Out-Null         | Discard output                 | | Out-Null                 |

================================================================================


================================================================================
                     WHERE-OBJECT SHORTCUTS
================================================================================

| Full Syntax                    | Shortcut                       |
|--------------------------------|--------------------------------|
| Where-Object { $_.X -eq 5 }    | Where-Object X -eq 5           |
| Where-Object { $_.Status }     | Where-Object Status            |
| ForEach-Object { $_.Name }     | ForEach-Object Name            |

Comparison operators in Where-Object:
    -eq, -ne        Equal, Not equal
    -gt, -lt        Greater than, Less than
    -ge, -le        Greater/Less or equal
    -like           Wildcard match
    -match          Regex match
    -contains       Collection contains
    -in             Value in collection

================================================================================


================================================================================
                     SELECT-OBJECT TECHNIQUES
================================================================================

| Technique              | Example                                        |
|------------------------|------------------------------------------------|
| Select properties      | Select-Object Name, Id                         |
| First N items          | Select-Object -First 10                        |
| Last N items           | Select-Object -Last 5                          |
| Skip N items           | Select-Object -Skip 3                          |
| Unique values          | Select-Object -Unique                          |
| Expand property        | Select-Object -ExpandProperty Items            |
| Calculated property    | Select-Object @{N='Size';E={$_.Length/1MB}}    |
| Exclude property       | Select-Object * -ExcludeProperty Password      |

================================================================================


================================================================================
                     GROUP-OBJECT TECHNIQUES
================================================================================

| Technique              | Example                                        |
|------------------------|------------------------------------------------|
| Group by property      | Group-Object Status                            |
| Group by expression    | Group-Object {$_.Name[0]}                      |
| No element grouping    | Group-Object Status -NoElement                 |
| As hash table          | Group-Object Status -AsHashTable               |
| As string              | Group-Object Status -AsString                  |

Accessing grouped data:
    $groups = Get-Service | Group-Object Status

    $groups.Name           # Group names
    $groups.Count          # Items per group
    $groups.Group          # Actual items in group

================================================================================


================================================================================
                     PIPELINE PERFORMANCE TIPS
================================================================================

1. Filter early (Where-Object before ForEach-Object)

    # Good - filter first
    Get-Process | Where-Object CPU -gt 10 | ForEach-Object { ... }

    # Bad - process all then filter
    Get-Process | ForEach-Object { ... } | Where-Object CPU -gt 10

2. Use -PipelineVariable for complex pipelines

    Get-Service -PipelineVariable svc |
        Where-Object Status -eq 'Running' |
        ForEach-Object { "$($svc.Name): $($_.DisplayName)" }

3. Avoid += in loops (use pipeline instead)

    # Good
    $result = Get-Process | ForEach-Object { $_.Name }

    # Bad (slow for large datasets)
    $result = @()
    Get-Process | ForEach-Object { $result += $_.Name }

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Invoke-WebRequest: The response content cannot be parsed"
Solution: Use -UseBasicParsing parameter
    Invoke-WebRequest -Uri $url -UseBasicParsing

Problem: Pipeline returns nothing
Solution: Check each stage with Tee-Object
    Get-Service |
        Tee-Object -Variable stage1 |
        Where-Object Status -eq 'Running' |
        Tee-Object -Variable stage2

Problem: "Cannot bind parameter" in pipeline
Solution: Ensure property names match
    Get-Process | Select-Object Name, Id  # Correct properties

Problem: Group-Object returns unexpected results
Solution: Check property type and value
    Get-Service | Group-Object Status | Select-Object Name, Count

Problem: Web request fails with SSL error
Solution: Ignore certificate errors (for testing only)
    [System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}

Problem: ForEach-Object variable scope
Solution: Use -PipelineVariable or store in outer scope
    $results = Get-Process | ForEach-Object {
        [PSCustomObject]@{Name = $_.Name}
    }

Problem: HTML content not parsed correctly
Solution: Use regex to strip HTML tags
    $content -replace '<[^>]+>', ' '

+----------------------------------------------------------+
|                    >>> SCREENSHOT 19 <<<                  |
|                                                          |
|  Show: Summary of pipeline tasks                         |
|  Expected: Both tasks completed successfully             |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Task 1: Get-CimInstance Win32_Service used to get service info
[ ] Task 1: Get-Process used to get process details
[ ] Task 1: Pipeline chains Where-Object and ForEach-Object
[ ] Task 1: Result stored in hash table with service names as keys
[ ] Task 1: Process info includes PID, name, memory
[ ] Task 2: Invoke-WebRequest fetches Wikipedia article
[ ] Task 2: HTML tags removed with -replace
[ ] Task 2: Text split into words with -split
[ ] Task 2: Words filtered by length (>3 characters)
[ ] Task 2: Group-Object counts word occurrences
[ ] Task 2: Sort-Object -Descending orders by count
[ ] Task 2: Select-Object -First 10 gets top words
[ ] Understand pipeline data flow (left to right)
[ ] Know how to use Where-Object for filtering
[ ] Know how to use ForEach-Object for transformation
[ ] Know how to use Group-Object for counting
[ ] Understand Select-Object calculated properties

================================================================================
