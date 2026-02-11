PROVIDERS - ANSWER
==================

This guide covers PowerShell providers, file system operations,
and reading/writing files using provider cmdlets.


================================================================================
                     POWERSHELL PROVIDERS OVERVIEW
================================================================================

What are Providers?
    Providers expose data stores as file system-like drives.
    They allow you to navigate and manipulate data using familiar commands.

Built-in Providers:
    FileSystem      Files and directories (C:, D:, etc.)
    Registry        Windows Registry (HKLM:, HKCU:)
    Environment     Environment variables (Env:)
    Alias           PowerShell aliases (Alias:)
    Function        PowerShell functions (Function:)
    Variable        PowerShell variables (Variable:)
    Certificate     X.509 certificates (Cert:)

View all providers:
    Get-PSProvider

View all drives:
    Get-PSDrive

================================================================================


TASK 1: Get 10 Longest Words and Save to File
-----------------------------------------------

Goal: Extract words from text, find 10 longest, save to separate file.

Sample input file (input.txt):

    PowerShell is a cross-platform task automation solution made up of
    a command-line shell, a scripting language, and a configuration
    management framework. PowerShell runs on Windows, Linux, and macOS.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Sample input.txt file                             |
|  Expected: Text content for processing                   |
+----------------------------------------------------------+

Solution - script1.ps1:

    param(
        [Parameter(Mandatory=$true)]
        [string]$InputFile,

        [string]$OutputFile = "longest_words.txt"
    )

    # Read file content using FileSystem provider
    $content = Get-Content -Path $InputFile -Raw

    # Extract words, sort by length, get top 10
    $longestWords = $content -split '\W+' |
        Where-Object { $_.Length -gt 0 } |
        Sort-Object -Property Length -Descending |
        Select-Object -First 10 -Unique

    # Create chart output
    $chart = @()
    $chart += "TOP 10 LONGEST WORDS"
    $chart += "=" * 40
    $chart += ""

    $rank = 1
    foreach ($word in $longestWords) {
        $bar = '#' * $word.Length
        $chart += "{0,2}. {1,-20} ({2,2} chars) {3}" -f $rank, $word, $word.Length, $bar
        $rank++
    }

    # Display to console
    $chart | ForEach-Object { Write-Host $_ }

    # Save to file using Set-Content
    $chart | Set-Content -Path $OutputFile

    Write-Host "`nSaved to: $OutputFile" -ForegroundColor Green

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: script1.ps1 content                               |
|  Expected: Complete script with chart formatting         |
+----------------------------------------------------------+

Test the script:

    .\script1.ps1 -InputFile "input.txt"

Expected output:

    TOP 10 LONGEST WORDS
    ========================================

     1. configuration       (13 chars) #############
     2. cross-platform      (14 chars) ##############
     3. automation          (10 chars) ##########
     4. PowerShell          (10 chars) ##########
     5. management          (10 chars) ##########
     6. scripting           (9 chars)  #########
     7. framework           (9 chars)  #########
     8. command-line        (12 chars) ############
     9. solution            (8 chars)  ########
    10. language            (8 chars)  ########

    Saved to: longest_words.txt

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Script execution output                           |
|  Expected: Formatted chart with bars                     |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Output file content                               |
|  Expected: longest_words.txt with chart                  |
+----------------------------------------------------------+

Alternative - Simple version:

    param(
        [string]$InputFile = "input.txt",
        [string]$OutputFile = "longest_words.txt"
    )

    Get-Content -Path $InputFile |
        ForEach-Object { $_ -split '\W+' } |
        Where-Object { $_ -match '^[a-zA-Z]+$' } |
        Sort-Object { $_.Length } -Descending |
        Select-Object -First 10 -Unique |
        Set-Content -Path $OutputFile

    Write-Host "Top 10 longest words saved to $OutputFile"
    Get-Content $OutputFile

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Simple version output                             |
|  Expected: Just words, no chart                          |
+----------------------------------------------------------+

Enhanced version with word count:

    param(
        [string]$InputFile = "input.txt",
        [string]$OutputFile = "longest_words.txt",
        [int]$TopN = 10
    )

    $content = Get-Content -Path $InputFile -Raw

    $wordStats = $content -split '\W+' |
        Where-Object { $_.Length -gt 0 -and $_ -match '^[a-zA-Z]+$' } |
        Group-Object |
        Select-Object @{N='Word';E={$_.Name}},
                      @{N='Length';E={$_.Name.Length}},
                      Count |
        Sort-Object Length -Descending |
        Select-Object -First $TopN

    # Output with statistics
    $output = @()
    $output += "LONGEST WORDS ANALYSIS"
    $output += "File: $InputFile"
    $output += "=" * 50
    $output += ""
    $output += "{0,-5} {1,-20} {2,-8} {3}" -f "Rank", "Word", "Length", "Count"
    $output += "{0,-5} {1,-20} {2,-8} {3}" -f "----", "----", "------", "-----"

    $rank = 1
    foreach ($item in $wordStats) {
        $output += "{0,-5} {1,-20} {2,-8} {3}" -f $rank, $item.Word, $item.Length, $item.Count
        $rank++
    }

    $output | Set-Content -Path $OutputFile
    $output | ForEach-Object { Write-Host $_ }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Enhanced version with word count                  |
|  Expected: Statistics table output                       |
+----------------------------------------------------------+


TASK 2: Sort Words into A-Z Files
----------------------------------

Goal: Read words from file, distribute into 26 alphabetical files.

Solution - script2.ps1:

    param(
        [Parameter(Mandatory=$true)]
        [string]$InputFile,

        [string]$OutputDirectory = ".\AlphabetWords"
    )

    # Create output directory if not exists
    if (-not (Test-Path -Path $OutputDirectory)) {
        New-Item -Path $OutputDirectory -ItemType Directory | Out-Null
        Write-Host "Created directory: $OutputDirectory" -ForegroundColor Cyan
    }

    # Read and extract words
    $content = Get-Content -Path $InputFile -Raw

    $words = $content -split '\W+' |
        Where-Object { $_.Length -gt 0 -and $_ -match '^[a-zA-Z]+$' } |
        ForEach-Object { $_.ToLower() } |
        Sort-Object -Unique

    Write-Host "Found $($words.Count) unique words" -ForegroundColor Yellow

    # Create A-Z files
    foreach ($letter in 'A'..'Z') {
        $filePath = Join-Path -Path $OutputDirectory -ChildPath "$letter.txt"

        # Filter words starting with this letter
        $letterWords = $words | Where-Object { $_.ToUpper().StartsWith($letter) }

        if ($letterWords) {
            $letterWords | Set-Content -Path $filePath
            Write-Host "$letter.txt : $($letterWords.Count) words" -ForegroundColor Green
        }
        else {
            # Create empty file
            Set-Content -Path $filePath -Value ""
            Write-Host "$letter.txt : 0 words" -ForegroundColor Gray
        }
    }

    Write-Host "`nCompleted! Files saved to: $OutputDirectory" -ForegroundColor Cyan

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: script2.ps1 content                               |
|  Expected: Complete A-Z distribution script              |
+----------------------------------------------------------+

Test the script:

    .\script2.ps1 -InputFile "input.txt"

Expected output:

    Created directory: .\AlphabetWords
    Found 45 unique words
    A.txt : 3 words
    B.txt : 0 words
    C.txt : 5 words
    D.txt : 1 words
    ...
    P.txt : 2 words
    ...
    Z.txt : 0 words

    Completed! Files saved to: .\AlphabetWords

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Script execution output                           |
|  Expected: Word count per letter file                    |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Output directory with A-Z files                   |
|  Expected: 26 .txt files created                         |
+----------------------------------------------------------+

View contents of specific file:

    Get-Content ".\AlphabetWords\P.txt"

Expected output:

    platform
    powershell

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Content of one letter file                        |
|  Expected: Words starting with that letter               |
+----------------------------------------------------------+

Alternative using Group-Object:

    param(
        [string]$InputFile = "input.txt",
        [string]$OutputDirectory = ".\AlphabetWords"
    )

    # Create directory
    New-Item -Path $OutputDirectory -ItemType Directory -Force | Out-Null

    # Read, clean, and group words by first letter
    Get-Content -Path $InputFile -Raw |
        ForEach-Object { $_ -split '\W+' } |
        Where-Object { $_ -match '^[a-zA-Z]+$' } |
        ForEach-Object { $_.ToLower() } |
        Sort-Object -Unique |
        Group-Object { $_.Substring(0,1).ToUpper() } |
        ForEach-Object {
            $letter = $_.Name
            $words = $_.Group | Sort-Object
            $filePath = Join-Path $OutputDirectory "$letter.txt"
            $words | Set-Content -Path $filePath
            Write-Host "$letter.txt created with $($words.Count) words"
        }

    # Create empty files for missing letters
    'A'..'Z' | ForEach-Object {
        $filePath = Join-Path $OutputDirectory "$_.txt"
        if (-not (Test-Path $filePath)) {
            Set-Content -Path $filePath -Value ""
            Write-Host "$_.txt created (empty)"
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Group-Object alternative                          |
|  Expected: More efficient grouping approach              |
+----------------------------------------------------------+

Enhanced version with statistics:

    param(
        [string]$InputFile = "input.txt",
        [string]$OutputDirectory = ".\AlphabetWords"
    )

    # Create directory
    if (-not (Test-Path $OutputDirectory)) {
        New-Item -Path $OutputDirectory -ItemType Directory | Out-Null
    }

    $content = Get-Content -Path $InputFile -Raw

    $allWords = $content -split '\W+' |
        Where-Object { $_ -match '^[a-zA-Z]+$' } |
        ForEach-Object { $_.ToLower() } |
        Sort-Object -Unique

    $stats = @()

    foreach ($letter in 'A'..'Z') {
        $filePath = Join-Path $OutputDirectory "$letter.txt"
        $letterWords = $allWords | Where-Object { $_[0] -eq $letter.ToLower() } | Sort-Object

        $count = if ($letterWords) { $letterWords.Count } else { 0 }
        $letterWords | Set-Content -Path $filePath

        $stats += [PSCustomObject]@{
            Letter = $letter
            Count = $count
            File = "$letter.txt"
        }
    }

    # Display statistics
    Write-Host "`nWORD DISTRIBUTION BY LETTER" -ForegroundColor Cyan
    Write-Host "=" * 40

    $stats | ForEach-Object {
        $bar = '#' * [math]::Min($_.Count, 30)
        "{0}: {1,3} {2}" -f $_.Letter, $_.Count, $bar
    }

    # Summary
    $totalWords = ($stats | Measure-Object -Property Count -Sum).Sum
    Write-Host "`nTotal: $totalWords words in 26 files" -ForegroundColor Green

    # Save statistics
    $stats | Export-Csv -Path (Join-Path $OutputDirectory "statistics.csv") -NoTypeInformation

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Enhanced version with visual statistics           |
|  Expected: Bar chart of word distribution                |
+----------------------------------------------------------+


COMPLETE SCRIPT FILES
---------------------

script1.ps1 (10 Longest Words):

    param(
        [Parameter(Mandatory=$true)]
        [string]$InputFile,
        [string]$OutputFile = "longest_words.txt"
    )

    # Read file content
    $content = Get-Content -Path $InputFile -Raw

    # Extract and sort words by length
    $longestWords = $content -split '\W+' |
        Where-Object { $_.Length -gt 0 -and $_ -match '^[a-zA-Z]+$' } |
        Sort-Object -Property Length -Descending |
        Select-Object -First 10 -Unique

    # Create chart
    $chart = @()
    $chart += "TOP 10 LONGEST WORDS"
    $chart += "=" * 40
    $chart += ""

    $rank = 1
    foreach ($word in $longestWords) {
        $bar = '#' * $word.Length
        $chart += "{0,2}. {1,-20} ({2,2} chars) {3}" -f $rank, $word, $word.Length, $bar
        $rank++
    }

    # Output
    $chart | ForEach-Object { Write-Host $_ }
    $chart | Set-Content -Path $OutputFile
    Write-Host "`nSaved to: $OutputFile" -ForegroundColor Green

script2.ps1 (A-Z Word Files):

    param(
        [Parameter(Mandatory=$true)]
        [string]$InputFile,
        [string]$OutputDirectory = ".\AlphabetWords"
    )

    # Create output directory
    if (-not (Test-Path -Path $OutputDirectory)) {
        New-Item -Path $OutputDirectory -ItemType Directory | Out-Null
    }

    # Read and process words
    $content = Get-Content -Path $InputFile -Raw

    $words = $content -split '\W+' |
        Where-Object { $_.Length -gt 0 -and $_ -match '^[a-zA-Z]+$' } |
        ForEach-Object { $_.ToLower() } |
        Sort-Object -Unique

    Write-Host "Processing $($words.Count) unique words..." -ForegroundColor Cyan

    # Create A-Z files
    foreach ($letter in 'A'..'Z') {
        $filePath = Join-Path -Path $OutputDirectory -ChildPath "$letter.txt"
        $letterWords = $words | Where-Object { $_.ToUpper().StartsWith($letter) }

        if ($letterWords) {
            $letterWords | Set-Content -Path $filePath
            Write-Host "$letter.txt : $($letterWords.Count) words" -ForegroundColor Green
        }
        else {
            Set-Content -Path $filePath -Value ""
            Write-Host "$letter.txt : 0 words" -ForegroundColor Gray
        }
    }

    Write-Host "`nCompleted! Files in: $OutputDirectory" -ForegroundColor Cyan

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
                     POWERSHELL PROVIDERS REFERENCE
================================================================================

| Provider      | Drive(s)      | Description                              |
|---------------|---------------|------------------------------------------|
| FileSystem    | C:, D:, etc.  | Files and folders                        |
| Registry      | HKLM:, HKCU:  | Windows Registry                         |
| Environment   | Env:          | Environment variables                    |
| Alias         | Alias:        | PowerShell aliases                       |
| Function      | Function:     | PowerShell functions                     |
| Variable      | Variable:     | PowerShell variables                     |
| Certificate   | Cert:         | X.509 certificates                       |

Provider cmdlets (work with any provider):
    Get-Item            Get single item
    Get-ChildItem       Get child items (dir, ls)
    Set-Item            Set item value
    New-Item            Create new item
    Remove-Item         Delete item
    Copy-Item           Copy item
    Move-Item           Move item
    Rename-Item         Rename item
    Test-Path           Check if path exists
    Get-Content         Read content
    Set-Content         Write content
    Add-Content         Append content

================================================================================


================================================================================
                     FILE SYSTEM CMDLETS REFERENCE
================================================================================

| Cmdlet          | Description                    | Example                  |
|-----------------|--------------------------------|--------------------------|
| Get-Content     | Read file content              | Get-Content file.txt     |
| Set-Content     | Write/overwrite file           | Set-Content file.txt $x  |
| Add-Content     | Append to file                 | Add-Content file.txt $x  |
| Clear-Content   | Clear file content             | Clear-Content file.txt   |
| Out-File        | Send output to file            | $x | Out-File file.txt   |
| New-Item        | Create file or directory       | New-Item -ItemType File  |
| Remove-Item     | Delete file or directory       | Remove-Item file.txt     |
| Copy-Item       | Copy file or directory         | Copy-Item src.txt dst.txt|
| Move-Item       | Move file or directory         | Move-Item old.txt new.txt|
| Test-Path       | Check if path exists           | Test-Path file.txt       |
| Resolve-Path    | Get full path                  | Resolve-Path .\file.txt  |
| Join-Path       | Combine path parts             | Join-Path $dir $file     |

================================================================================


================================================================================
                     GET-CONTENT PARAMETERS
================================================================================

| Parameter       | Description                    | Example                  |
|-----------------|--------------------------------|--------------------------|
| -Path           | File path                      | -Path "file.txt"         |
| -Raw            | Read as single string          | -Raw                     |
| -TotalCount     | Read first N lines             | -TotalCount 10           |
| -Tail           | Read last N lines              | -Tail 5                  |
| -Encoding       | File encoding                  | -Encoding UTF8           |
| -Delimiter      | Custom line delimiter          | -Delimiter ","           |
| -Wait           | Wait for new content           | -Wait (like tail -f)     |

Examples:

    # Read entire file as one string
    $content = Get-Content -Path "file.txt" -Raw

    # Read first 10 lines
    $lines = Get-Content -Path "file.txt" -TotalCount 10

    # Read last 5 lines
    $lines = Get-Content -Path "file.txt" -Tail 5

    # Read with specific encoding
    $content = Get-Content -Path "file.txt" -Encoding UTF8

================================================================================


================================================================================
                     SET-CONTENT VS OUT-FILE
================================================================================

Set-Content:
    - Designed for text data
    - Default encoding: system default
    - Accepts pipeline input
    - Faster for simple writes

Out-File:
    - Designed for formatted output
    - Default encoding: Unicode
    - Preserves formatting from Format-* cmdlets
    - More options for width/encoding

Examples:

    # Set-Content - simple text
    "Hello World" | Set-Content -Path "file.txt"
    Set-Content -Path "file.txt" -Value $lines

    # Out-File - formatted output
    Get-Process | Format-Table | Out-File -Path "processes.txt"
    Get-Process | Out-File -Path "processes.txt" -Width 200

================================================================================


================================================================================
                     PATH MANIPULATION
================================================================================

| Cmdlet/Method   | Description                    | Example                  |
|-----------------|--------------------------------|--------------------------|
| Join-Path       | Combine path parts             | Join-Path "C:\temp" "f.txt"|
| Split-Path      | Get path components            | Split-Path "C:\temp\f.txt" |
| Resolve-Path    | Get absolute path              | Resolve-Path ".\file.txt"|
| Test-Path       | Check existence                | Test-Path "C:\temp"      |
| [IO.Path]::     | .NET path methods              | [IO.Path]::GetExtension()|

Split-Path options:
    -Parent         Get parent directory
    -Leaf           Get file name only
    -Extension      Get extension (PS 6+)
    -LeafBase       Get name without extension (PS 6+)

Examples:

    $path = "C:\Users\John\Documents\file.txt"

    Split-Path $path -Parent      # C:\Users\John\Documents
    Split-Path $path -Leaf        # file.txt

    [IO.Path]::GetExtension($path)     # .txt
    [IO.Path]::GetFileNameWithoutExtension($path)  # file

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Cannot find path" error
Solution: Verify path exists with Test-Path
    if (Test-Path $path) { Get-Content $path }

Problem: File encoding issues (special characters)
Solution: Specify encoding explicitly
    Get-Content -Path $file -Encoding UTF8
    Set-Content -Path $file -Encoding UTF8 -Value $content

Problem: "Access denied" error
Solution: Check file permissions, run as admin if needed
    # Check if file is read-only
    (Get-Item $file).IsReadOnly

Problem: Path with spaces not working
Solution: Quote the path
    Get-Content -Path "C:\My Documents\file.txt"

Problem: New-Item fails because directory exists
Solution: Use -Force parameter
    New-Item -Path $dir -ItemType Directory -Force

Problem: Words not separating correctly
Solution: Use better regex pattern
    $content -split '\W+'           # Split on non-word chars
    $content -split '\s+'           # Split on whitespace only

Problem: Empty lines in output file
Solution: Filter empty strings
    $words | Where-Object { $_.Length -gt 0 } | Set-Content $file

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Summary of provider tasks                         |
|  Expected: Both tasks completed successfully             |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Task 1: Get-Content used to read input file
[ ] Task 1: Words extracted using -split
[ ] Task 1: Words sorted by length descending
[ ] Task 1: Top 10 longest words selected
[ ] Task 1: Set-Content used to save output file
[ ] Task 1: Chart format includes word, length, visual bar
[ ] Task 2: Input file read successfully
[ ] Task 2: Words cleaned and made lowercase
[ ] Task 2: Output directory created with New-Item
[ ] Task 2: 26 files created (A.txt through Z.txt)
[ ] Task 2: Each file contains words starting with that letter
[ ] Task 2: Empty files created for letters with no words
[ ] Understand PowerShell providers concept
[ ] Know how to use Get-Content and Set-Content
[ ] Know how to use Test-Path and New-Item
[ ] Understand Join-Path for path construction

================================================================================
