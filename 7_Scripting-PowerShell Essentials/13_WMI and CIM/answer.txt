WMI AND CIM - ANSWER
====================

This guide covers Windows Management Instrumentation (WMI) and
Common Information Model (CIM) for system management and querying.


================================================================================
                     WMI AND CIM OVERVIEW
================================================================================

WMI vs CIM:
    WMI (Windows Management Instrumentation)
        - Legacy technology
        - Uses DCOM protocol
        - Get-WmiObject cmdlet (deprecated)

    CIM (Common Information Model)
        - Modern replacement for WMI
        - Uses WS-Man protocol (WinRM)
        - Get-CimInstance cmdlet (recommended)

Common CIM cmdlets:
    Get-CimInstance      Query CIM classes
    Get-CimClass         Get class information
    Invoke-CimMethod     Call methods
    New-CimSession       Create remote session
    Remove-CimSession    Close remote session

Common WMI/CIM classes:
    Win32_Processor           CPU information
    Win32_PhysicalMemory      RAM modules
    Win32_OperatingSystem     OS information
    Win32_LogicalDisk         Disk drives
    Win32_Service             Windows services
    Win32_QuickFixEngineering Installed hotfixes
    Win32_LogonSession        Logon sessions
    Win32_Account             User accounts

================================================================================


TASK 1: Get Remote Computer System Information
-----------------------------------------------

Goal: Retrieve CPU, RAM, disk, OS, hotfixes, and services from remote computer.

Complete script1.ps1:

    param(
        [Parameter(Mandatory=$false)]
        [string]$ComputerName = $env:COMPUTERNAME,

        [System.Management.Automation.PSCredential]$Credential
    )

    Write-Host "=" * 60 -ForegroundColor Cyan
    Write-Host "SYSTEM INFORMATION REPORT" -ForegroundColor Cyan
    Write-Host "Computer: $ComputerName" -ForegroundColor Cyan
    Write-Host "Date: $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')" -ForegroundColor Cyan
    Write-Host "=" * 60 -ForegroundColor Cyan

    # Create CIM session for remote computer
    $cimParams = @{
        ComputerName = $ComputerName
    }
    if ($Credential) {
        $cimParams['Credential'] = $Credential
    }

    try {
        # For remote computers, create a CIM session
        if ($ComputerName -ne $env:COMPUTERNAME) {
            $session = New-CimSession @cimParams
            $cimParams = @{ CimSession = $session }
        }
        else {
            $cimParams = @{}
        }

        # ============================================
        # CPU INFORMATION
        # ============================================
        Write-Host "`n[CPU INFORMATION]" -ForegroundColor Yellow
        Write-Host "-" * 40

        $cpu = Get-CimInstance -ClassName Win32_Processor @cimParams

        foreach ($proc in $cpu) {
            Write-Host "Model:          $($proc.Name)"
            Write-Host "Manufacturer:   $($proc.Manufacturer)"
            Write-Host "Max Clock Speed: $($proc.MaxClockSpeed) MHz"
            Write-Host "Current Speed:  $($proc.CurrentClockSpeed) MHz"
            Write-Host "Cores:          $($proc.NumberOfCores)"
            Write-Host "Logical CPUs:   $($proc.NumberOfLogicalProcessors)"
        }

        # ============================================
        # RAM INFORMATION
        # ============================================
        Write-Host "`n[RAM INFORMATION]" -ForegroundColor Yellow
        Write-Host "-" * 40

        $os = Get-CimInstance -ClassName Win32_OperatingSystem @cimParams

        $totalRAM = [math]::Round($os.TotalVisibleMemorySize / 1MB, 2)
        $freeRAM = [math]::Round($os.FreePhysicalMemory / 1MB, 2)
        $usedRAM = [math]::Round($totalRAM - $freeRAM, 2)
        $percentUsed = [math]::Round(($usedRAM / $totalRAM) * 100, 1)

        Write-Host "Total RAM:      $totalRAM GB"
        Write-Host "Free RAM:       $freeRAM GB"
        Write-Host "Used RAM:       $usedRAM GB ($percentUsed%)"

        # Physical memory modules
        $memory = Get-CimInstance -ClassName Win32_PhysicalMemory @cimParams
        Write-Host "Memory Modules: $($memory.Count)"

        # ============================================
        # OS DISK FREE SPACE
        # ============================================
        Write-Host "`n[DISK INFORMATION]" -ForegroundColor Yellow
        Write-Host "-" * 40

        $disks = Get-CimInstance -ClassName Win32_LogicalDisk @cimParams |
            Where-Object { $_.DriveType -eq 3 }  # Local disks only

        foreach ($disk in $disks) {
            $totalGB = [math]::Round($disk.Size / 1GB, 2)
            $freeGB = [math]::Round($disk.FreeSpace / 1GB, 2)
            $usedGB = [math]::Round($totalGB - $freeGB, 2)
            $percentFree = [math]::Round(($freeGB / $totalGB) * 100, 1)

            Write-Host "Drive $($disk.DeviceID)"
            Write-Host "  Total:     $totalGB GB"
            Write-Host "  Free:      $freeGB GB ($percentFree%)"
            Write-Host "  Used:      $usedGB GB"
        }

        # ============================================
        # OS VERSION
        # ============================================
        Write-Host "`n[OPERATING SYSTEM]" -ForegroundColor Yellow
        Write-Host "-" * 40

        Write-Host "OS Name:        $($os.Caption)"
        Write-Host "Version:        $($os.Version)"
        Write-Host "Build Number:   $($os.BuildNumber)"
        Write-Host "Architecture:   $($os.OSArchitecture)"
        Write-Host "Install Date:   $($os.InstallDate)"
        Write-Host "Last Boot:      $($os.LastBootUpTime)"

        # ============================================
        # INSTALLED HOT FIXES
        # ============================================
        Write-Host "`n[INSTALLED HOT FIXES]" -ForegroundColor Yellow
        Write-Host "-" * 40

        $hotfixes = Get-CimInstance -ClassName Win32_QuickFixEngineering @cimParams |
            Sort-Object InstalledOn -Descending |
            Select-Object -First 10

        Write-Host "Recent hotfixes (last 10):"
        foreach ($fix in $hotfixes) {
            $installedDate = if ($fix.InstalledOn) {
                $fix.InstalledOn.ToString('yyyy-MM-dd')
            } else { 'Unknown' }
            Write-Host "  $($fix.HotFixID) - $($fix.Description) - $installedDate"
        }

        $totalHotfixes = (Get-CimInstance -ClassName Win32_QuickFixEngineering @cimParams).Count
        Write-Host "`nTotal hotfixes installed: $totalHotfixes"

        # ============================================
        # STOPPED SERVICES
        # ============================================
        Write-Host "`n[STOPPED SERVICES]" -ForegroundColor Yellow
        Write-Host "-" * 40

        $stoppedServices = Get-CimInstance -ClassName Win32_Service @cimParams |
            Where-Object { $_.State -eq 'Stopped' -and $_.StartMode -eq 'Auto' }

        Write-Host "Auto-start services that are stopped:"
        foreach ($svc in $stoppedServices | Select-Object -First 15) {
            Write-Host "  [$($svc.State)] $($svc.Name) - $($svc.DisplayName)"
        }

        if ($stoppedServices.Count -gt 15) {
            Write-Host "  ... and $($stoppedServices.Count - 15) more"
        }

        Write-Host "`nTotal stopped auto-start services: $($stoppedServices.Count)"

        # Close CIM session if created
        if ($session) {
            Remove-CimSession -CimSession $session
        }

    }
    catch {
        Write-Host "Error: $_" -ForegroundColor Red
        if ($session) {
            Remove-CimSession -CimSession $session
        }
    }

    Write-Host "`n" + "=" * 60 -ForegroundColor Cyan
    Write-Host "END OF REPORT" -ForegroundColor Cyan
    Write-Host "=" * 60 -ForegroundColor Cyan

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: script1.ps1 content                               |
|  Expected: Complete system info script                   |
+----------------------------------------------------------+

Test the script (local computer):

    .\script1.ps1

Test the script (remote computer):

    .\script1.ps1 -ComputerName "Server01"

    # With credentials
    $cred = Get-Credential
    .\script1.ps1 -ComputerName "Server01" -Credential $cred

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Script execution                                  |
|  Expected: System information displayed                  |
+----------------------------------------------------------+

Expected output:

    ============================================================
    SYSTEM INFORMATION REPORT
    Computer: DESKTOP-ABC123
    Date: 2024-01-15 14:30:45
    ============================================================

    [CPU INFORMATION]
    ----------------------------------------
    Model:          Intel(R) Core(TM) i7-9700 CPU @ 3.00GHz
    Manufacturer:   GenuineIntel
    Max Clock Speed: 3000 MHz
    Current Speed:  3000 MHz
    Cores:          8
    Logical CPUs:   8

    [RAM INFORMATION]
    ----------------------------------------
    Total RAM:      15.93 GB
    Free RAM:       8.45 GB
    Used RAM:       7.48 GB (47%)
    Memory Modules: 2

    [DISK INFORMATION]
    ----------------------------------------
    Drive C:
      Total:     237.88 GB
      Free:      125.32 GB (52.7%)
      Used:      112.56 GB

    [OPERATING SYSTEM]
    ----------------------------------------
    OS Name:        Microsoft Windows 10 Pro
    Version:        10.0.19045
    Build Number:   19045
    Architecture:   64-bit
    ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: CPU and RAM output                                |
|  Expected: Formatted system information                  |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Disk, OS, hotfixes, services output               |
|  Expected: Complete report sections                      |
+----------------------------------------------------------+

Alternative - Export to HTML report:

    param(
        [string]$ComputerName = $env:COMPUTERNAME,
        [string]$OutputFile = "SystemReport.html"
    )

    $html = @"
    <html>
    <head>
        <title>System Report - $ComputerName</title>
        <style>
            body { font-family: Arial; }
            table { border-collapse: collapse; width: 100%; }
            th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
            th { background-color: #4CAF50; color: white; }
            h2 { color: #333; }
        </style>
    </head>
    <body>
    <h1>System Report: $ComputerName</h1>
    "@

    # Add sections to HTML...
    $cpu = Get-CimInstance Win32_Processor
    $html += "<h2>CPU</h2><p>$($cpu.Name) - $($cpu.MaxClockSpeed) MHz</p>"

    $html += "</body></html>"
    $html | Set-Content $OutputFile

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: HTML report alternative                           |
|  Expected: Formatted HTML output                         |
+----------------------------------------------------------+


TASK 2: Get Logon Sessions with User Information
--------------------------------------------------

Goal: Get interactive and remote logon sessions with associated user info.

Logon Types reference:
    0  - System
    2  - Interactive (local logon)
    3  - Network
    4  - Batch
    5  - Service
    7  - Unlock
    8  - NetworkCleartext
    9  - NewCredentials
    10 - RemoteInteractive (RDP)
    11 - CachedInteractive

Complete script2.ps1:

    param(
        [Parameter(Mandatory=$false)]
        [string]$ComputerName = $env:COMPUTERNAME,

        [System.Management.Automation.PSCredential]$Credential
    )

    Write-Host "=" * 60 -ForegroundColor Cyan
    Write-Host "LOGON SESSIONS REPORT" -ForegroundColor Cyan
    Write-Host "Computer: $ComputerName" -ForegroundColor Cyan
    Write-Host "Date: $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')" -ForegroundColor Cyan
    Write-Host "=" * 60 -ForegroundColor Cyan

    # Logon type descriptions
    $logonTypes = @{
        0  = "System"
        2  = "Interactive"
        3  = "Network"
        4  = "Batch"
        5  = "Service"
        7  = "Unlock"
        8  = "NetworkCleartext"
        9  = "NewCredentials"
        10 = "RemoteInteractive"
        11 = "CachedInteractive"
    }

    # CIM session parameters
    $cimParams = @{}
    if ($ComputerName -ne $env:COMPUTERNAME) {
        $sessionParams = @{ ComputerName = $ComputerName }
        if ($Credential) { $sessionParams['Credential'] = $Credential }
        $session = New-CimSession @sessionParams
        $cimParams['CimSession'] = $session
    }

    try {
        # Get logon sessions - Interactive (2) and RemoteInteractive (10)
        Write-Host "`n[INTERACTIVE AND REMOTE LOGON SESSIONS]" -ForegroundColor Yellow
        Write-Host "-" * 50

        $sessions = Get-CimInstance -ClassName Win32_LogonSession @cimParams |
            Where-Object { $_.LogonType -in @(2, 10) }

        if (-not $sessions) {
            Write-Host "No interactive or remote sessions found." -ForegroundColor Gray
        }

        $sessionCount = 0
        foreach ($session in $sessions) {
            $sessionCount++

            # Get associated user account using WMI association
            $query = "ASSOCIATORS OF {Win32_LogonSession.LogonId='$($session.LogonId)'} WHERE AssocClass=Win32_LoggedOnUser"
            $user = Get-CimInstance -Query $query @cimParams

            Write-Host "`n--- Session $sessionCount ---" -ForegroundColor Green

            # Session Information
            Write-Host "Session ID:     $($session.LogonId)"
            Write-Host "Logon Type:     $($session.LogonType) ($($logonTypes[$session.LogonType]))"
            Write-Host "Start Time:     $($session.StartTime)"
            Write-Host "Auth Package:   $($session.AuthenticationPackage)"

            # User Information
            if ($user) {
                Write-Host "`nUser Information:" -ForegroundColor Cyan
                Write-Host "  Account:      $($user.Name)"
                Write-Host "  Domain:       $($user.Domain)"
                Write-Host "  Full Name:    $($user.FullName)"
                Write-Host "  SID:          $($user.SID)"
                Write-Host "  Status:       $($user.Status)"
                Write-Host "  Account Type: $($user.AccountType)"

                # Get additional user details from Win32_Account
                $account = Get-CimInstance -ClassName Win32_Account @cimParams |
                    Where-Object { $_.SID -eq $user.SID }

                if ($account) {
                    Write-Host "  Description:  $($account.Description)"
                    Write-Host "  Local:        $($account.LocalAccount)"
                }
            }
            else {
                Write-Host "`nUser Information: Not available" -ForegroundColor Gray
            }
        }

        # ============================================
        # ALL SESSIONS SUMMARY
        # ============================================
        Write-Host "`n" + "-" * 50 -ForegroundColor Yellow
        Write-Host "[ALL LOGON SESSIONS SUMMARY]" -ForegroundColor Yellow
        Write-Host "-" * 50

        $allSessions = Get-CimInstance -ClassName Win32_LogonSession @cimParams

        $sessionSummary = $allSessions |
            Group-Object LogonType |
            ForEach-Object {
                [PSCustomObject]@{
                    LogonType = $_.Name
                    TypeName = $logonTypes[[int]$_.Name]
                    Count = $_.Count
                }
            } |
            Sort-Object Count -Descending

        $sessionSummary | Format-Table -AutoSize

        # ============================================
        # CURRENTLY LOGGED ON USERS
        # ============================================
        Write-Host "[CURRENTLY LOGGED ON USERS]" -ForegroundColor Yellow
        Write-Host "-" * 50

        $loggedOnUsers = Get-CimInstance -ClassName Win32_LoggedOnUser @cimParams

        $uniqueUsers = $loggedOnUsers |
            Select-Object -ExpandProperty Antecedent |
            Select-Object -Property Domain, Name -Unique

        foreach ($u in $uniqueUsers) {
            # Parse the user info
            if ($u.Domain -and $u.Name) {
                Write-Host "  $($u.Domain)\$($u.Name)"
            }
        }

        # Alternative: Parse from CIM instance
        $loggedOnUsers | ForEach-Object {
            $ant = $_.Antecedent
            if ($ant -match 'Domain="([^"]+)".*Name="([^"]+)"') {
                $domain = $Matches[1]
                $name = $Matches[2]
                Write-Host "  User: $domain\$name" -ForegroundColor White
            }
        } | Select-Object -Unique

        # ============================================
        # DETAILED USER ACCOUNTS
        # ============================================
        Write-Host "`n[USER ACCOUNTS ON SYSTEM]" -ForegroundColor Yellow
        Write-Host "-" * 50

        $accounts = Get-CimInstance -ClassName Win32_UserAccount @cimParams |
            Where-Object { $_.LocalAccount -eq $true }

        foreach ($acct in $accounts) {
            $status = if ($acct.Disabled) { "Disabled" } else { "Enabled" }
            Write-Host "  $($acct.Name) [$status] - $($acct.FullName)"
        }

        # Close session
        if ($session) {
            Remove-CimSession -CimSession $session
        }

    }
    catch {
        Write-Host "Error: $_" -ForegroundColor Red
        if ($session) {
            Remove-CimSession -CimSession $session
        }
    }

    Write-Host "`n" + "=" * 60 -ForegroundColor Cyan
    Write-Host "END OF REPORT" -ForegroundColor Cyan
    Write-Host "=" * 60 -ForegroundColor Cyan

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: script2.ps1 content                               |
|  Expected: Complete logon session script                 |
+----------------------------------------------------------+

Test the script:

    .\script2.ps1

    # Remote computer
    .\script2.ps1 -ComputerName "Server01"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Script execution                                  |
|  Expected: Logon sessions displayed                      |
+----------------------------------------------------------+

Expected output:

    ============================================================
    LOGON SESSIONS REPORT
    Computer: DESKTOP-ABC123
    Date: 2024-01-15 14:30:45
    ============================================================

    [INTERACTIVE AND REMOTE LOGON SESSIONS]
    --------------------------------------------------

    --- Session 1 ---
    Session ID:     12345678
    Logon Type:     2 (Interactive)
    Start Time:     1/15/2024 9:00:00 AM
    Auth Package:   NTLM

    User Information:
      Account:      JohnDoe
      Domain:       DESKTOP-ABC123
      Full Name:    John Doe
      SID:          S-1-5-21-...
      Status:       OK
      Account Type: 512

    --- Session 2 ---
    Session ID:     87654321
    Logon Type:     10 (RemoteInteractive)
    Start Time:     1/15/2024 10:30:00 AM
    Auth Package:   Kerberos

    User Information:
      Account:      Admin
      Domain:       CORP
      ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Session details output                            |
|  Expected: User info associated with sessions            |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Session summary and logged on users               |
|  Expected: Summary table and user list                   |
+----------------------------------------------------------+

Alternative - Compact version:

    param([string]$ComputerName = $env:COMPUTERNAME)

    # Get sessions with users in one query
    Get-CimInstance -ClassName Win32_LogonSession -ComputerName $ComputerName |
        Where-Object { $_.LogonType -in @(2, 10) } |
        ForEach-Object {
            $session = $_
            $query = "ASSOCIATORS OF {Win32_LogonSession.LogonId='$($_.LogonId)'} WHERE AssocClass=Win32_LoggedOnUser"
            $user = Get-CimInstance -Query $query -ComputerName $ComputerName

            [PSCustomObject]@{
                SessionId = $session.LogonId
                LogonType = switch ($session.LogonType) {
                    2 { "Interactive" }
                    10 { "RemoteInteractive" }
                }
                StartTime = $session.StartTime
                UserName = $user.Name
                Domain = $user.Domain
            }
        } | Format-Table -AutoSize

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Compact version output                            |
|  Expected: Table format of sessions                      |
+----------------------------------------------------------+


COMPLETE SCRIPT FILES
---------------------

script1.ps1 (System Information - Simplified):

    param(
        [string]$ComputerName = $env:COMPUTERNAME
    )

    Write-Host "System Report for: $ComputerName`n" -ForegroundColor Cyan

    # CPU
    $cpu = Get-CimInstance Win32_Processor -ComputerName $ComputerName
    Write-Host "[CPU] $($cpu.Name) - Max: $($cpu.MaxClockSpeed) MHz"

    # RAM
    $os = Get-CimInstance Win32_OperatingSystem -ComputerName $ComputerName
    $totalGB = [math]::Round($os.TotalVisibleMemorySize / 1MB, 2)
    $freeGB = [math]::Round($os.FreePhysicalMemory / 1MB, 2)
    Write-Host "[RAM] Total: $totalGB GB, Free: $freeGB GB"

    # Disk
    Get-CimInstance Win32_LogicalDisk -ComputerName $ComputerName |
        Where-Object DriveType -eq 3 |
        ForEach-Object {
            $free = [math]::Round($_.FreeSpace / 1GB, 2)
            Write-Host "[DISK] $($_.DeviceID) Free: $free GB"
        }

    # OS
    Write-Host "[OS] $($os.Caption) - $($os.Version)"

    # Hotfixes
    $fixes = (Get-CimInstance Win32_QuickFixEngineering -ComputerName $ComputerName).Count
    Write-Host "[HOTFIXES] $fixes installed"

    # Stopped Services
    $stopped = Get-CimInstance Win32_Service -ComputerName $ComputerName |
        Where-Object { $_.State -eq 'Stopped' -and $_.StartMode -eq 'Auto' }
    Write-Host "[SERVICES] $($stopped.Count) stopped (auto-start)"

script2.ps1 (Logon Sessions - Simplified):

    param(
        [string]$ComputerName = $env:COMPUTERNAME
    )

    Write-Host "Logon Sessions for: $ComputerName`n" -ForegroundColor Cyan

    Get-CimInstance Win32_LogonSession -ComputerName $ComputerName |
        Where-Object { $_.LogonType -in @(2, 10) } |
        ForEach-Object {
            $session = $_
            $query = "ASSOCIATORS OF {Win32_LogonSession.LogonId='$($_.LogonId)'} WHERE AssocClass=Win32_LoggedOnUser"
            $user = Get-CimInstance -Query $query -ComputerName $ComputerName

            [PSCustomObject]@{
                SessionId = $session.LogonId
                Type = if ($session.LogonType -eq 2) { "Interactive" } else { "Remote" }
                StartTime = $session.StartTime
                User = "$($user.Domain)\$($user.Name)"
            }
        } | Format-Table -AutoSize

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Both simplified scripts                           |
|  Expected: Compact working versions                      |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Both scripts tested                               |
|  Expected: Successful execution                          |
+----------------------------------------------------------+


================================================================================
                     COMMON WMI/CIM CLASSES REFERENCE
================================================================================

| Class                        | Description                              |
|------------------------------|------------------------------------------|
| Win32_Processor              | CPU information                          |
| Win32_PhysicalMemory         | RAM modules                              |
| Win32_OperatingSystem        | OS details, memory totals                |
| Win32_LogicalDisk            | Disk drives and space                    |
| Win32_DiskDrive              | Physical disk drives                     |
| Win32_Service                | Windows services                         |
| Win32_Process                | Running processes                        |
| Win32_QuickFixEngineering    | Installed hotfixes                       |
| Win32_ComputerSystem         | Computer information                     |
| Win32_NetworkAdapterConfig   | Network configuration                    |
| Win32_LogonSession           | Logon sessions                           |
| Win32_LoggedOnUser           | Logged on users association              |
| Win32_Account                | User/group accounts                      |
| Win32_UserAccount            | User accounts only                       |

================================================================================


================================================================================
                     CIM CMDLETS REFERENCE
================================================================================

| Cmdlet              | Description                    | Example               |
|---------------------|--------------------------------|-----------------------|
| Get-CimInstance     | Query CIM class                | Get-CimInstance Win32_OS|
| Get-CimClass        | Get class metadata             | Get-CimClass Win32_*  |
| Invoke-CimMethod    | Call class method              | Invoke-CimMethod ...  |
| New-CimSession      | Create remote session          | New-CimSession -CN srv|
| Remove-CimSession   | Close session                  | Remove-CimSession $s  |
| Get-CimAssociatedInstance | Get associated instances | Get-CimAssociatedInstance|

Remote connection:

    # Using ComputerName (DCOM fallback)
    Get-CimInstance Win32_OS -ComputerName "Server01"

    # Using CimSession (WS-Man)
    $session = New-CimSession -ComputerName "Server01"
    Get-CimInstance Win32_OS -CimSession $session
    Remove-CimSession $session

    # With credentials
    $cred = Get-Credential
    $session = New-CimSession -ComputerName "Server01" -Credential $cred

================================================================================


================================================================================
                     WMI QUERY LANGUAGE (WQL)
================================================================================

Basic query:

    Get-CimInstance -Query "SELECT * FROM Win32_Service"

Filtered query:

    Get-CimInstance -Query "SELECT * FROM Win32_Service WHERE State='Running'"

Association query:

    Get-CimInstance -Query "ASSOCIATORS OF {Win32_LogonSession.LogonId='12345'}"

Examples:

    # Get stopped services
    Get-CimInstance -Query "SELECT * FROM Win32_Service WHERE State='Stopped'"

    # Get local disks only
    Get-CimInstance -Query "SELECT * FROM Win32_LogicalDisk WHERE DriveType=3"

    # Get specific user
    Get-CimInstance -Query "SELECT * FROM Win32_UserAccount WHERE Name='Admin'"

================================================================================


================================================================================
                     LOGON TYPES REFERENCE
================================================================================

| Type | Name                | Description                              |
|------|---------------------|------------------------------------------|
| 0    | System              | System account logon                     |
| 2    | Interactive         | Local console logon                      |
| 3    | Network             | Network logon (file shares)              |
| 4    | Batch               | Scheduled task logon                     |
| 5    | Service             | Service account logon                    |
| 7    | Unlock              | Workstation unlock                       |
| 8    | NetworkCleartext    | Network logon with cleartext             |
| 9    | NewCredentials      | RunAs with different credentials         |
| 10   | RemoteInteractive   | RDP/Remote Desktop logon                 |
| 11   | CachedInteractive   | Cached credentials logon                 |

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Access denied" on remote computer
Solution: Ensure WinRM is enabled and you have permissions
    # On remote computer (as admin)
    Enable-PSRemoting -Force
    winrm quickconfig

Problem: "RPC server is unavailable"
Solution: Check firewall and WMI service
    # Allow WMI through firewall
    netsh advfirewall firewall set rule group="Windows Management Instrumentation (WMI)" new enable=yes

Problem: CIM session fails with DCOM error
Solution: Use explicit DCOM protocol option
    $opt = New-CimSessionOption -Protocol Dcom
    $session = New-CimSession -ComputerName "Server01" -SessionOption $opt

Problem: "Invalid class" error
Solution: Verify class name exists
    Get-CimClass -ClassName Win32_* | Select-Object CimClassName

Problem: StartTime is null for some sessions
Solution: System sessions may not have start time
    if ($session.StartTime) { $session.StartTime } else { "N/A" }

Problem: Association query returns nothing
Solution: Verify LogonId format in query
    $query = "ASSOCIATORS OF {Win32_LogonSession.LogonId='$($session.LogonId)'}"

Problem: Slow performance on remote queries
Solution: Use CIM session for multiple queries
    $session = New-CimSession -ComputerName $server
    # Multiple queries with same session
    Get-CimInstance Win32_OS -CimSession $session
    Get-CimInstance Win32_Processor -CimSession $session
    Remove-CimSession $session

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Summary of WMI/CIM tasks                          |
|  Expected: Both tasks completed successfully             |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Task 1: Get-CimInstance used to query Win32 classes
[ ] Task 1: Win32_Processor returns CPU model and clock speed
[ ] Task 1: Win32_OperatingSystem returns RAM info
[ ] Task 1: Win32_LogicalDisk returns disk space
[ ] Task 1: Win32_QuickFixEngineering returns hotfixes
[ ] Task 1: Win32_Service filtered for stopped services
[ ] Task 1: Script works with -ComputerName parameter
[ ] Task 2: Win32_LogonSession returns logon sessions
[ ] Task 2: Filtered for LogonType 2 and 10
[ ] Task 2: ASSOCIATORS query links session to user
[ ] Task 2: Win32_Account provides user details
[ ] Task 2: Each session shows associated user information
[ ] Understand WMI vs CIM differences
[ ] Know how to create CIM sessions for remote queries
[ ] Understand WQL query syntax
[ ] Know common Win32 class names

================================================================================
